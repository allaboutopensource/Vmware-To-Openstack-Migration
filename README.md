# Vmware-To-Openstack-Migration

VMware to OpenStack Migration Using VEXXHOST MigrationKit.

This technical guide walks through the process of exporting a virtual machine from VMware vSphere/ESXi and importing it into OpenStack using VEXXHOST's OpenStack MigrationKit.

https://github.com/vexxhost/migratekit


![image](https://github.com/user-attachments/assets/4db67035-eb23-44eb-bee5-84d015f6a201)

Prerequisites

A running VMware vSphere or ESXi environment

Access to the VM to be migrated

Access to an OpenStack environment (with appropriate credentials)

Linux system or VM with:

Python 3.6+

Docker (for using prebuilt containers)

Migratekit is a CLI tool which can help you to migrate your virtual machines from VMware to OpenStack in a near-zero downtime. It runs as an container on docker, to pull a docker image 

nbdkit with the VDDK plugin is used to access VMware VMDK disk images directly from ESXi or vCenter servers. 

docker pull ghcr.io/vexxhost/migratekit:main

Change block tracking (CBT) on the Vmware disks needs to be the enabled. For every disks it should be enabled.

Environment variable should be source and should have admin privileges on the openstack cloud

Migration kit Vm should be running on openstack cloud and this vm should have docker installed. 

Download VDDK  (Virtual Disk Development Kit) from VMware and extract it to :

/usr/lib64/vmware-vix-disklib/

To enable CBT on vmware vm:
•	Right-click the VM in vSphere Client.
•	Click Edit Settings.
•	Go to the Options tab.
•	Expand Advanced > General.
•	Click Configuration Parameters->Add parameters

ctkEnabled true

scsi0:0.ctkEnabled true

source openrc.sh (Ensure that rc file contains : export OS_DOMAIN_NAME=Cloud)

Note: Either domainname or domainID should be defined not both

wget https://dp-downloads.broadcom.com/VMware-vix-disklib-8.0.0-20521017.x86_64.tar.gz

gunzip VMware-vix-disklib-8.0.0-20521017.x86_64.tar.gz

Run MigrationKit via Docker. You will also need to make sure you have all of your OpenStack environment variables set in your environment before running the command so that Migratekit can connect to the OpenStack cloud.

docker run -it --rm --privileged   --network host -v /dev/disk/by-id:/dev/disk/by-id  -v /dev:/dev   -v /usr/lib64/vmware-vix-disklib/:/usr/lib64/vmware-vix-disklib:ro   --env-file <(env | grep OS_)   ghcr.io/vexxhost/migratekit:main   migrate    --vmware-endpoint <ip address> --vmware-username root --vmware-password 'secret2123'   --vmware-path /ha-datacenter/vm/migration-vm --debug

The vm snapshot will be created on the vmware side and then it will do a full copy of the vmdk to the volume in openstack. The migrated volume(qcow2) gets attached to the conversion vm on openstack and detached later. Finally the snapshot will be removed from the vmware side. 
