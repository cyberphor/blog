---
layout: post
title: 'ACAS: Deployment Guide'
permalink: 'acas-deployment-guide'
category: notes
subcategory: guides
---

### Table of Contents
* [Links](#links)
* [Deploying ACAS](#deploying-acas)
* [Access ACAS SecurityCenter](#access-acas-securitycenter)
* [Configure an ACAS Nessus Scanner](#configure-an-acas-nessus-scanner)
* [Configure a Repository](#configre-a-repository)
* [Configure an Organization](#configre-an-organization)
* [Configure a Security Manager](#configure-a-security-manager)

### Links
* [Download CentOS 6.9](http://archive.kernel.org/centos-vault/6.9/isos/x86_64/CentOS-6.9-x86_64-LiveDVD.iso)
* [Download ACAS (Tenable.sc & Nessus Scanner)](https://patches.csd.disa.mil/CollectionInfo.aspx)
* [DISA ACAS (License) Request Portal](https://disa.deps.mil/ext/cop/mae/netops/acas/Requests/index.aspx#/)
  * [Army Naming Convention and Standards (Annex C)]( https://army.deps.mil/netcom/sites/resourcecenter/pages/cinamingconventions.aspx)

### Deploying ACAS
1. Install CentOS 6.9 OS
2. Download ACAS (Tenable.sc & Nessus Scanner) binaries
3. Install ACAS binaries
5. Browse to `https://localhost`

```bash
# step 3
yum install CM-235553-SecurityCenter-5.8.0-el6.x86_64.rpm -y 
yum install CM-238325-Nessus-7.2.2-es6.x86_64.rpm -y
```

### Access ACAS SecurityCenter 
```bash
# https://localhost
# run `netstat -pant` to verify there is a port open for ACAS
# Supply the <license-matching-your-hostname>.key file
```

### Configure an ACAS Nessus Scanner
```bash
- Name: scanner01
- Host: 192.168.56.106 (Host-only IP for personal lab environment)
- Port: 8834
- Username: victor (must match the user you create when you setup Nessus)
- Password: <password>
```

### Configure a Repository
```bash
- Name: repo01
- Description: "Nodes within my (Host-only) virtual lab"
- IP ranges: 192.168.56.0/
```

### Configure an Organization
```bash
- Name: organization01
- Description: "..."
```

### Configure a Security Manager
```bash
# 255S
- First Name: Victor
- Last Name:  Fernandez
- Username: victor
- Password: <password>
- Administrator (255A): <password>
- # username for default admin is "admin"
```

### Start Nessus scanner
```bash
service nessusd start
```

### Setup Nessus Scanner (change IP/URL to where it's located; ex: localhost)
```bash
# access via https://localhost:8834
- Username: victor
- Password: <password>
- Scanner Type: Managed By SecurityCenter
```

### Overview of ACAS
ACAS is a collection vulnerability, port, and patch compliance scanners
- SecurityCenter (SC) = web interface to manage/execute ACAS functions
	- WIN-T increment 1b = 1 SC per BDE 
- Nessus Scanner = establish one per scanner
	- Downloaded plugin feeds must be younger than 7 days in order to be within DoD compliance
	- WIN-T increment 1b = 1 SC per BDE
	- WIN-T increment 1b = 1 SC per BN
- Passive Vulnerability Scanner (PVS) = a sensor (like tcpdump, snort)
	- Not really used (was also replaced with a similar tool)

Doctrine mandating the use of ACAS
- Task order 13-670
- Task order 14-0294
- DISA's ACAS TTP

* DISA published Request For Proposal in 2013 to replace Retina Scanner
* HP responded with Nessus Scanner, Passive Vulnerability Scanner, & SecurityCenter 

**ACAS Roles**
- Administrator (255A) = manages users & groups
- Security manager (255S) = manages scans, blackout windows, updating plugins, etc and other users (except admins)
- Security analyst (25D) = manages scans, etc. but CANNOT manage users
- Auditor (255S at an RCC level org) = checks if scan activities are being done correctly
- Executive (25/26/17A) = can read high-level view of reports 

- ACAS is a Program of Record (managed by DISA)
- Every unit should have an ACAS server
- ACAS (SecurityCenter) software licenses are managed by DISA
	- https://disa.deps.mil/extcop/mae/netops/acas/Lists/ACAS_License_Request/Submit.aspx
	- Submit a form (type of license; unit)
	- License expires after a year
- Usually installed on Red Hat (which requires another license, a unit responsibility)
	- CentOS is an alternative (but not the newest version, 7)
	- Can download a Red Hat VM from DISA with ACAS pre-installed 

**ACAS can scan for:**
* Vulnerabilities (app, system, network)
* Device configuration
* Network discovery
* Compliance with SCAP (Security Content Automation Protocol)

**ACAS Components**
* SecurityCenter
    * Uses 8835 to connect to SecurityCenter 
    * GUI; central console for ACAS and data; where to update plugins
* Nessus Scanner
    * Uses 8834
    * Active passive scanner
    * Compliance scanner (STIG, SCAP, etc.)
    * Scans data at rest
* Passive Vulnerability Scanner
    * Uses 8835 to connect to SecurityCenter 
    * Sniffs network for traffic via tap/span port; continuous monitoring solution
    * Uses scripts/plugins to detect vulnerabilities on the network passively
    * Scans data in transit/motion
* 3D
    * Uses 443 (can change)
    * Relies on pre-built queries to generate visuals of vulnerabilities (like Kibana)
    * Password-based authentication; if you require CAC, can't login

**ACAS functions**
* Discover assets via Ping, port scanners, traceroute, TTLs
* Vulnerabilities are tied to the script/plugins that find them
    * Vulns come from poor configuration (unneeded ports open), implementation (buffer overflows), or design (IP, Telnet, etc)
    * ACAS plugins are developed by DISA using info from Tenable (via Bugtraq, OSVDB, CVE)

**ACAS Task Orders**
* Task Order 13-670 Implementation of ACAS for the Enterprise
* Task Order 14-0294 IAVM Reporting Compliance Using CMRS
* DISA's ACAS Tactics, Techniques, and Procedures

### SecurityCenter 5.3
**How to Change Your Default Timezone**
1. Click-on Username drop-down menu
2. Click-on Profile
3. Click-on preferred Time Zone

# Menus
* Navigation bar
    * Dashboard: first button upon login
    * Analysis: 
        * Vulnerabilities
        * Events: not supported by DISA right now; for log correlation 
        * Mobile: MDM servers
        * Queries: saved filters
    * Scans
        * Active scans: vulnerability scan, compliance scans
        * Agent scans
        * Scan results
        * Policies: how scan will run (plugins, ports to scan, performance options)
        * Audit files: compliance docs (.xml files with STIG checks)
        * Credentials: for login to host being scanned (SSH)
        * Blackout windows: set "do not scan" during these windows (or assets at certain times)
    * Reporting
        * Reports
        * Report Results
        * Report Images: company logos
        * Report Attributes: scan data destined for CMRS (continuous monitoring risk score)
    * Workflow
        * Alerts: scan for specific vulnerability, if found, send an alert to someone
        * Tickets: lightweight system for issue management
        * Accept Risk Rules: mark a vulnerability as accepted
        * Recast Risk Rules: 
    * Users
        * Users:
        * Roles: "what I can do"
        * Groups: "what I can see"
* Username drop-down menu
    * About: SecurityCenter version
    * System Logs: login events, filter by severity, etc.
    * Profile: change password, change Time Zone
        * Responsibility: designate Assets by admin name ("Vic is responsible for Linux Hosts")
    * Feeds: can connect to DISA or DoD patch repo for the latest plugins (downloaded as tar/zip files)
    * Notifications
    * Plugins: can write & upload custom scripts/plugins using Nessus Attack Scripting Language (NASL) using top-right button; plugin update time can be viewed here and in Feeds
    * Help
    * Logout

### SecurityCenter Building Blocks
* Structure (within a single instance of SecurityCenter)
    * roles per org (group of people responsible for common assets): 1 admin, 2 security managers, 4 analysts 
    * structure designs: based on mission (directorate 1, directorate 2, tactical); CONUS/OCONUS; report authority (ARCENT v CENTCOM) 
* Role, Group, User definitions
    * Role: functional permissions
    * Group: access controls
    * Users: user details/assignments
* Scan Zone (and Nessus Scanners)
    * Scan Zone = specified subnet in SecurityCenter (if you select an IP to scan, SecurityCenter will select the appropriate Nessus Scanner based on the Scan Zone) 

### Repos, Zones, and Plugins
* Repository: folder with text-based scan files (containing IP addresses of hosts to scan). 
    * Max-size is 32GBs
* Zones: area where scans are performed
* Admin manages Repos, Zones, and Plugins
    * ex: Zone 1 references Repo A (a text-file with IPs) to know who to scan
    * can add multiple IP ranges to a single Repo (so more than one Zone can use it)
    * Repos are individual from Zones
* Plugins
    * How do you get your plugins?
        * Vendor > DISA > DISA Plugin Server > Scheduled pull > SecurityCenter > Auto push > Nessus scanner
    * How to search for plugins
        * Can search for by Plugin family (MS Bulletins, device type, etc.)
        * Can search by CVE ID, Bugtraq ID (BID), Name, etc. 
            1.  Select "Name" by drop-down menu
            2. Type keyword
            3. Click-on **Apply**
    * Plugin Ids (click on the "i" button to view source-code of plugins/scripts)
        * Passive scanning plugins (PVS): 1-10,000 
        * Active/Nessus plugins: 10,001-900,000 
        * Custom plugins: 900,001-999,999 
        * Compliance plugins: 1,000,000+ 

### Scanning a Stand-Alone network
* Option 1: Install Nessus and SecurityCenter on RHEL laptop via Kickstart
    * Easy deployment; faster than Windows version of Nessus
* Option 2: RHEL VMs (Nessus, SecurityCenter) on Windows laptop
    * Can use Windows browser to access SecurityCenter
* Option 3: Detach Nessus Scanner from ACAS system for Stand-Alone network
    * Must re-attach scanner (RHEL laptop) to get plugin updates
    * Miss out on full functionality
