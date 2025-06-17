# Vmware-To-Openstack-Migration

VMware to OpenStack Migration Using VEXXHOST MigrationKit

This technical guide walks through the process of exporting a virtual machine from VMware vSphere/ESXi and importing it into OpenStack using VEXXHOST's OpenStack MigrationKit.

Prerequisites

A running VMware vSphere or ESXi environment

Access to the VM to be migrated

Access to an OpenStack environment (with appropriate credentials)

Linux system or VM with:

Python 3.6+

libvirt and QEMU tools installed

Docker (for using prebuilt containers)

Migratekit is a CLI tool which can help you to migrate your virtual machines from VMware to OpenStack in a near-zero downtime. It runs as an container on docker, to pull a docker image 

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

source openrc.sh
wget https://dp-downloads.broadcom.com/VMware-vix-disklib-8.0.0-20521017.x86_64.tar.gz
gunzip VMware-vix-disklib-8.0.0-20521017.x86_64.tar.gz
extract to the location : /usr/lib64/vmware-vix-disklib/lib64/

Run MigrationKit via Docker

docker run -it --rm --privileged   --network host -v /dev/disk/by-id:/dev/disk/by-id  -v /dev:/dev   -v /usr/lib64/vmware-vix-disklib/:/usr/lib64/vmware-vix-disklib:ro   --env-file <(env | grep OS_)   ghcr.io/vexxhost/migratekit:main   migrate    --vmware-endpoint <ip address> --vmware-username root --vmware-password 'secret2123'   --vmware-path /ha-datacenter/vm/migration-vm --debug
