Raspy K8s Project
=================

Kubernetes installation on heterogeneous Raspberry cluster. Managed by ansible. No need for desktop Raspbian version, every step of the installation process is automated.
You just need an extra software for SD Card flashing (Etcher for example)

## Introduction

Hardware:

* 3x Raspberry Pi 3 Model B
* 2x Raspberry Pi Zero WH
* 2x Raspberry Pi Zero W
* class 10 SD Cards with heterogeneous storage capacity and i/o capabilities

Technical environment:

* Linux workstation with root/sudo access, if you want to see linux partitions on SD cards ;)
* Ansible 2.4
* 7x nodes cluster of Raspberry powered by Raspbian Stretch Lite
* Kubernetes 1.9 with kubeadm, 1 master, 6 workers

Main goals:

* Automation for raspberry provisioning
* prepare SD Cards before first boot
* install, upgrade and maintain OS
* manage kubeadm installation

## Requirements

1. Download Raspbian image on https://www.raspberrypi.org/downloads/
2. Flash your SD Cards with the downloaded image, give a try to [Etcher](https://etcher.io/)
3. Plug a SD Card into your card reader, you should see two partitions mounted ("boot" and "rootfs")

## Discovering the sd card mountpoint

Run df -h to see which devices are currently mounted.

If your computer has a slot for SD cards, insert the card. If not, insert the card into an SD card reader, then connect the reader to your computer.

Run df -h again. The new device that has appeared is your SD card. If no device appears, then your system is not automounting devices. In this case, you will need to search for the device name using another method. The  dmesg | tail command will display the most recent system messages, which should contain information on the naming of the SD card device.

The left column of the results from df -h command gives the device name of your SD card. It will be listed as something like /dev/mmcblk0p1 or /dev/sdX1, where X is a lower case letter indicating the device. The last part (p1 or 1 respectively) is the partition number. You want to write to the whole SD card, not just one partition. You therefore need to remove that section from the name. You should see something like /dev/mmcblk0 or  /dev/sdX as the device name for the whole SD card. Note that the SD card can show up more than once in the output of df. It will do this if you have previously written a Raspberry Pi image to this SD card, because the Raspberry Pi SD images have more than one partition.

## Before the first boot, the first playbook

Customize ansible global configuration (group vars, host vars, encrypted vars, whatever vars...) :

```yaml

raspy_sd_boot: /media/<username>/boot
raspy_sd_rootfs: /media/<username>/rootfs

raspy_wifi_ssid: changeIt
raspy_wifi_password: changeIt

raspy_admin_user: pi
raspy_admin_user_group: pi
raspy_admin_authorized_key: "REPLACE IT WITH YOUR RSA PUBLIC KEY" # default:  "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"

raspy_hostname: raspy01
raspy_ip: 192.168.0.81
raspy_router: 192.168.0.1
raspy_dns: 192.168.0.1
raspy_wlan_interface: wlan0

```

I encrypted wifi ssid and password with ansible-vault, change it to fit your needs
Also change the public key to deploy on pi user as the only authorized key.

Customize cluster member configuration in extra_vars/*.json :

```json
{
    "raspy_hostname": "raspy01",
    "raspy_ip": "192.168.0.81"
}
```

Launch the playbook :

```bash
cd ansible
ansible-playbook raspy-boot.yml --ask-vault-pass --ask-sudo-pass --extra-vars "@extra_vars/raspy01.json" -i inventories/localhost
```

The playbook will make some modifications on the base raspbian image, on both partitions (boot and rootfs) :

* [/boot] enable ssh (ssh empty file)
* [/boot] enable wifi (wpa-supplicant.conf)
* [/rootfs] add a public key to authorized keys
* [/rootfs] disallow password authentication on pi user
* [/rootfs] set raspy hostname
* [/rootfs] dhcp config for static ip
