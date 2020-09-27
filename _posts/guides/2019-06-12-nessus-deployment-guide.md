---
layout: post
title: 'Nessus: Deployment Guide'
permalink: 'nessus-deployment-guide'
category: notes
subcategory: guides
---

### Table of Contents
* [Deploying Nessus](#deploy-nessus)
* [How to Grant a User Administrator Privileges in CentOS](#how-to-grant-a-user-administrator-privileges-in-centos)
* [How to Change the Hostname in CentOS](#how-to-change-the-username-in-centos)
* [How to Assign a Static IP Address in CentOS](#how-to-assign-a-static-ip-address-in-centos)
* [References](#references)

### How to Grant a User Administrator Privileges in CentOS
```bash
su root
usermod -aG wheel victor # add the user to the 'wheel' group

visudo 
    %wheel ALL=(ALL) ALL # uncommment this line
```

### How to Change the Hostname in CentOS
```bash
# step 1
sudo vim /etc/sysconfig/network
    HOSTNAME=localhost # remove
    HOSTNAME=foxhound # add; ex: foxhound = new hostname

# step 2
sudo reboot now
```

### How to Assign a Static IP Address in CentOS
```bash
cd /etc/sysconfig/network-scripts/
sudo vim ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=none
    PREFIX=24
    IPADDR=192.168.1.69 # desired static ip
        
sudo service network restart
ping 192.168.1.1 # ping your gateway
ping 192.168.2.10 # ping something beyond your gateway
```

### Deploying Nessus
1. Identify a hostname for your Tenable.sc server (ex: `<Site><Organizational Code><System Type><Variable>`)
2. Request a Tenable.sc license (ex: from the [DISA ACAS License Request Portal](https://disa.deps.mil/ext/cop/mae/netops/acas/Requests/index.aspx#/))
3. Install the [CentOS 6.9](http://archive.kernel.org/centos-vault/6.9/isos/x86_64/CentOS-6.9-x86_64-LiveDVD.iso) operating system
4. Download, install, and start the [Tenable.sc & Nessus Scanner](https://patches.csd.disa.mil/CollectionInfo.aspx) binaries on the same machine you installed CentOS
    ```bash
    rpm -i CM-243951-SecurityCenter-5.11.0-el6.x86_64.rpm
    rpm -i CM-253106-Nessus-8.11.0-es6.x86_64.rpm
    service SecurityCenter status
    service SecurityCenter start
    service nessusd status
    service nessusd start 
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

### References
* [Army Naming Convention and Standards (Annex C)]( https://army.deps.mil/netcom/sites/resourcecenter/pages/cinamingconventions.aspx)
* [DISA ACAS License Request Portal](https://disa.deps.mil/ext/cop/mae/netops/acas/Requests/index.aspx#/)
* [Download CentOS 6.9](http://archive.kernel.org/centos-vault/6.9/isos/x86_64/CentOS-6.9-x86_64-LiveDVD.iso)
* [Tenable.sc & Nessus Scanner](https://patches.csd.disa.mil/CollectionInfo.aspx)
