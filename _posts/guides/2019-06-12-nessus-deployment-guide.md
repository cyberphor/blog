---
layout: post
title: 'Nessus: Deployment Guide'
permalink: 'nessus-deployment-guide'
category: notes
subcategory: guides
---

### Table of Contents
* [Overview](#overview)
* [Configuring a Nessus Scanner in SecurityCenter](#configuring-a-nessus-scanner-in-securitycenter)
* [Configuring a Repository](#configuring-a-repository)
* [Configuring an Organization](#configuring-an-organization)
* [Configuring a Security Manager](#configuring-a-security-manager)
* [Changing the Default Timezone](#changing-the-default-timezone)
* [Starting Nessus](#starting-nessus)
* [References & DoD Download Links](#references-dod-download-links)

### Overview
1. Request a license (ex: from the [DISA ACAS License Request Portal](https://disa.deps.mil/ext/cop/mae/netops/acas/Requests/index.aspx#/))
2. Install the [CentOS 6.9](http://archive.kernel.org/centos-vault/6.9/isos/x86_64/CentOS-6.9-x86_64-LiveDVD.iso) operating system
3. Download and install the [Nessus Scanner & SecurityCenter](https://patches.csd.disa.mil/CollectionInfo.aspx) binaries
    ```bash
    yum install CM-235553-SecurityCenter-5.8.0-el6.x86_64.rpm -y 
    yum install CM-238325-Nessus-7.2.2-es6.x86_64.rpm -y
    ```
4. Access SecurityCenter by browsing to `https://localhost`
    ```bash
    # run `netstat -pant` to verify there is a port open for ACAS
    # Supply the <license-matching-your-hostname>.key file
    ```

### Configuring a Nessus Scanner in SecurityCenter
```bash
- Name: scanner01
- Host: 192.168.56.106 (Host-only IP for personal lab environment)
- Port: 8834
- Username: victor (must match the user you create when you setup Nessus)
- Password: <password>
```

### Configuring a Repository
```bash
- Name: repo01
- Description: "Nodes within my (Host-only) virtual lab"
- IP ranges: 192.168.56.0/
```

### Configuring an Organization
```bash
- Name: organization01
- Description: "..."
```

### Configuring a Security Manager
```bash
# 255S
- First Name: Victor
- Last Name:  Fernandez
- Username: victor
- Password: <password>
- Administrator (255A): <password>
- # username for default admin is "admin"
```

### Changing the Default Timezone
1. Click-on Username drop-down menu
2. Click-on Profile
3. Click-on preferred Time Zone

### Starting Nessus
```bash
service nessusd start
```

### ACAS Task Orders
* Task Order 13-670 Implementation of ACAS for the Enterprise
* Task Order 14-0294 IAVM Reporting Compliance Using CMRS
* DISA's ACAS Tactics, Techniques, and Procedures

### References & DoD Download Links
* [DISA ACAS License Request Portal](https://disa.deps.mil/ext/cop/mae/netops/acas/Requests/index.aspx#/)
* [Army Naming Convention and Standards (Annex C)]( https://army.deps.mil/netcom/sites/resourcecenter/pages/cinamingconventions.aspx)
* [Download CentOS 6.9](http://archive.kernel.org/centos-vault/6.9/isos/x86_64/CentOS-6.9-x86_64-LiveDVD.iso)
* [Download Nessus Scanner & Tenable.sc](https://patches.csd.disa.mil/CollectionInfo.aspx)

