---
layout: post
title: 'Nessus: Deployment Guide'
permalink: 'nessus-deployment-guide'
category: notes
subcategory: guides
---

### Table of Contents
* [Deploying Tenable.sc and Nessus](#deploying-tenablesc-and-nessus)
* [Granting Someone "Super-User" Privileges in CentOS](#granting-someone-super-user-privileges-in-centos)
* [Installing VirtualBox Guest Additions in CentOS](#installing-virtualbox-guest-additions-in-centos)
* [Changing the Hostname in CentOS](#changing-the-hostname-in-centos)
* [Configuring a Static IP Address in CentOS](#assigning-a-static-ip-address-in-centos)
* [References](#references)

### Deploying Tenable.sc and Nessus
1. Identify a hostname for your Tenable.sc server
2. Request a Tenable.sc license (option 1: [DISA ACAS License Request Portal](https://disa.deps.mil/ext/cop/mae/netops/acas/Requests/index.aspx#/))
3. Install the [CentOS 6.9](http://archive.kernel.org/centos-vault/6.9/isos/x86_64/CentOS-6.9-x86_64-LiveDVD.iso) operating system ([change the hostname](#how-to-change-the-hostname-in-centos), [configure a static IP address](#how-to-assign-a-static-ip-address-in-centos), and specify a DNS server)
4. Download, install, and start the [Tenable.sc & Nessus Scanner](https://patches.csd.disa.mil/CollectionInfo.aspx) binaries on the same machine you installed CentOS
    ```
    sudo mkdir /opt/downloads/
    sudo cp ./*.rpm ./*.key /opt/downloads/
    cd /opt/downloads
    sudo rpm -i ./CM-243951-SecurityCenter-5.11.0-el6.x86_64.rpm
    sudo rpm -i ./CM-253106-Nessus-8.11.0-es6.x86_64.rpm
    sudo service SecurityCenter status
    sudo service SecurityCenter start
    sudo service nessusd status
    sudo service nessusd start 
    ```
5. Browse to `https://localhost:8834` to configure the Nessus Scanner
6. Create an account on the Nessus Scanner
7. Browse to the Tenable.sc web interface using `https://localhost`
8. Upload and activate the Tenable.sc license (rename the key file so it matches the Tenable.sc server hostname; ex: `hostname.key`)
9. Configure Tenable.sc
    * Add a Nessus Scanner
    * Configure a Repository
    * Configure an Organization
    * Configure a Security Manager

### Granting Someone "Super-User" Privileges in CentOS
```bash
# step 1
su root
usermod -aG wheel victor # add the user to the 'wheel' group

# step 2
visudo 
    %wheel ALL=(ALL) ALL # uncommment this line
    # use ':wq!' to exit the Vim text-editor
```

### Installing VirtualBox Guest Additions in CentOS
```bash
# step 1
sudo yum install kernel kernel-devel gcc make perl
sudo reboot now

# step 2 (make sure the current kernel and downloaded one match)
uname -r 
ls /usr/src/kernels/ 

# step 3
# click-on "Insert Guest Additions CD image..." from the Devices drop-menu in VirtualBox

# step 4
cd /media/VBox_GAs_6.0.6 # change directories to where Guest Additions is mounted
sudo ./autorun.sh
sudo reboot now

# step 5
sudo usermod -aG vboxsf victor # do this to access folders shared between the host and guest
sudo reboot now # or logout the GUI shell
```

### Changing the Hostname in CentOS
```bash
# step 1
sudo vim /etc/sysconfig/network
    HOSTNAME=localhost.localdomain # remove
    HOSTNAME=foxhound # add; ex: foxhound = new hostname

# step 2
sudo reboot now
```

### Assigning a Static IP Address in CentOS
```bash
# step 1
cd /etc/sysconfig/network-scripts/
sudo vim ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=none
    PREFIX=24
    IPADDR=192.168.3.69 # desired static ip
    DNS1=192.168.3.10 # local DNS server

# step 2
sudo service network restart

# step 3
ping 192.168.3.1 # ping your gateway
ping 192.168.3.10 # ping your DNS server
ping 8.8.8.8 # ping something beyond your gateway
```

### References
* [Army Naming Convention and Standards (Annex C)]( https://army.deps.mil/netcom/sites/resourcecenter/pages/cinamingconventions.aspx)
* [DISA ACAS License Request Portal](https://disa.deps.mil/ext/cop/mae/netops/acas/Requests/index.aspx#/)
* [Download CentOS 6.9](http://archive.kernel.org/centos-vault/6.9/isos/x86_64/CentOS-6.9-x86_64-LiveDVD.iso)
* [Tenable.sc & Nessus Scanner](https://patches.csd.disa.mil/CollectionInfo.aspx)
