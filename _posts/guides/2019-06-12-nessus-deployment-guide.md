---
layout: post
title: 'Nessus: Deployment Guide'
permalink: 'nessus-deployment-guide'
category: notes
subcategory: guides
---

### Table of Contents
* [Overview](#overview)
* [References](#references-dod-download-links)

### Overview
1. Identify a hostname for your Tenable.sc server (ex: `<Site><Organizational Code><System Type><Variable>`)
2. Request a Tenable.sc license (ex: from the [DISA ACAS License Request Portal](https://disa.deps.mil/ext/cop/mae/netops/acas/Requests/index.aspx#/))
3. Install the [CentOS 6.9](http://archive.kernel.org/centos-vault/6.9/isos/x86_64/CentOS-6.9-x86_64-LiveDVD.iso) operating system
4. Download, install, and start the [Tenable.sc & Nessus Scanner](https://patches.csd.disa.mil/CollectionInfo.aspx) binaries on the same machine you installed CentOS
    ```bash
    rpm -i CM-243951-SecurityCenter-5.8.0-el6.x86_64.rpm -y 
    rpm -i CM-2-Nessus-7.2.2-es6.x86_64.rpm -y
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

