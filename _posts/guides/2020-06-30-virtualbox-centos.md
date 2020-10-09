---
layout: post
title: 'VirtualBox: CentOS'
category: guides
permalink: 'guides/virtualbox-centos'
---

### Table of Contents
* [Changing the hostname in CentOS](#changing-the-hostname-in-centos)
* [Configuring 3 DHCP NICs in CentOS](#configuring-3-dhcp-nics-in-centos)
* [Create an alias in CentOS](#create-an-alias-in-centos)
* [Install kernel headers in CentOS](#install-kernel-headers-in-centos)
* [Install VirtualBox Guest Additions for CentOS](#install-virtualbox-guest-additions-for-centos)
* [Add user to VirtualBox group to access shared folders](#add-user-to-virtualbox-group-to-access-shared-folders)

### Changing the hostname in CentOS
```bash
# step 0
su root

# step 1 (disable this bogus service)
chkconfig NetworkManager off

# step 2 (specify name in file)
vim /etc/sysconfig/network
(update this line) ---> HOSTNAME=<new hostname>

# step 3 (reboot)
reboot now
```

### Configuring 3 DHCP NICs in CentOS
```bash
# Create a configuration file for each NIC
# I ended up having to use a NAT network, but the same process is necessary
[Host-only] vim /etc/sysconfig/network-scripts/ifcfg-eth0
[Bridged] vim /etc/sysconfig/network-scripts/ifcfg-eth1
[NAT] vim /etc/sysconfig/network-scripts/ifcfg-eth2

# Use the following syntax
NAME=Host-only (or NAT, Bridged)
DEVICE=eth0 (or eth1, eth2)
ONBOOT=yes
BOOTPROTO=dhcp

# Restart the services to bring up the interfaces
service network restart

# References
# https://www.cyberciti.biz/faq/setting-up-a-linux-for-dhcp/
```

### Create an alias in CentOS
```
# Open the appropiate file
vim /etc/bashrc

# Add your alias 
alias cls='clear'
```

### Install kernel headers in CentOS
```
# install the latest headers
yum install kernel-devel

# install the matching kernel
yum install kernel

# ensure the kernel and kernel headers downloaded match
uname -r 
ls /usr/src/kernels/
reboot
```

### Install VirtualBox Guest Additions for CentOS
```
# Ensure you have a valid network connection
ping www.google.com

# Ensure you have kernel headers installed
<see above>

# Update and upgrade
yum update

# Install the "gcc, make, and perl" libraries
yum install gcc make perl

# Identify the directory where the scripts are mounted to 
mount | grep VBox
cd /path/to/VBox/scripts/

# Run the Vbox script for Linux from the correct directory
./VBoxLinuxAdditions.run
reboot

# Manually change the resolution if desired

# References
# http://ask.xmodulo.com/install-kernel-headers-linux.html
```

### Add user to VirtualBox group to access shared folders
```
# Verify the name of the group
cat /etc/group

# Add user to the proper group
su root
usermod -a -G vboxsf

# Logout of the shell/GUI
```
