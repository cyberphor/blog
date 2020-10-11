---
layout: post
title: 'McAfee ePO: Deployment Guide'
category: guides
permalink: 'guides/mcafee-ePO/deployment'
---

## Table of Contents
* [HBSS Overview](#hbss-overview)
* [Creating Agent Deployment URLs for Clients](#creating-agent-deployment-urls-for-clients)
* [Removing an HBSS Client](#removing-an-hbss-client)
* [Creating a Client Task](#creating-a-client-task)
* [Running an Assigned Client Task manually](#running-an-assigned-client-task-manually)
* [Deleting an Assigned Client Task](#deleting-an-assigned-client-task)
* [Changing the McAfee Endpoint Security interface password](#changing-the-mcafee-endpoint-security-interface-password)
* [Updating Firewall Rules](#updating-firewall-rules)

## HBSS Overview
* HBSS (Host-based System Security): an end-point security product
	- Anti-virus solution
	- Intrusion prevention solution
	- Data loss prevention solution
	- DoD Program of Record, acquired from McAfee
	- Requires a license
		- Downloads as nine compressed files (must input a password in order to unzip & compile into an .iso file)
* ePolicy Orchestrater (ePO): a server used to manage how HBSS clients function
	- Windows Server R2 & SQL Server 2014
		- OS
		- Apps
		- Logs
		- Database
	- Integrates with ESM (a SIEM) 
* Remote Registry service on client must running
* Firewall on client/server must all remote connections

### Creating Agent Deployment URLs for Clients
```bash
1. Click-on System Tree
2. Click-on the Agent Deployment
3. Click-on the Create Agent Deployment URL button
	- URL Name: student10_OU_deploymentURL
	- Agent version: McAfee Agent for Windows 5.0.5 (Current)
	- Assign to Agent Handlers: All Agent Handlers
- Copy & paste URL using client's web browser
```

### Removing an HBSS Client
```bash
# Create a task to remove packages first
1. Click-on System Tree > Select your OU
	- Click-on the Systems tab
2. Select the client
3. Click-on the Actions drop-down menu (at the bottom)
4. Click-on Directory Management > Delete
5. [On client] Open the McAfee Agent Status Monitor
	- Click-on Collect and Send Props
```

### Creating a Client Task 
(ex: force install of ENS Modules)
```bash
1. Click-on System Tree > Select your OU
	- Click-on the Assigned Client Tasks tab
3. Click-on the New Client Task Assignment button (at the bottom)
4. Highlight: Tasks to Schedule > McAfee Agent > Product Deployment 
5. Click-on the Create New Task button in the Task Actions section
	- Task Name: 
		- student10_ClientTask_ENSPlatform_10.6.1
		- student10_ClientTask_ENSThreatPrevention_10.6.1
		- student10_ClientTask_ENSFirewall_10.6.1
	- Target platforms: Windows
	- Products and components: 
		- Endpoint Security Platform 10.6.1
		- Endpoint Security Threat Prevention 10.6.1	
		- Endpoint Security Firewall 10.6.1
	- Tags: Send this task to all computers
	- Schedule status: Enabled
	- Schedule type: Run immediately
6. [On client] Open the McAfee Agent Status Monitor
	- Click-on Collect and Send Props
```

### Running an Assigned Client Task manually
```bash
1. Click-on System Tree > Select your OU
	- Click-on the Assigned Client Tasks tab
```

### Deleting an Assigned Client Task
```bash
1. Click-on System Tree > Select your OU
	- Click-on the Systems tab
2. Select your client
3. Click-on the Actions drop-down menu > Agent > Run Client Task Now
4. Highlight Product: McAfee > Task Type: Product Deployment > Task Name: student10_ClientTask_ENSThreatPrevention_10.6.1
	- Click-on the Run Task Now button (at the bottom right)
```

### Changing the McAfee Endpoint Security interface password
```bash
1. Click-on System Tree > Select your OU
	- Click-on the Assigned Policies tab
2. Click-on All in the Product drop-down menu > DISA Stig ENS Options Policy (Policy column)
	- Click-on the Duplicate button
		- Name: student10_ENSPolicy 
		- Client Interface Mode: Lock client interface
			- Password: <password>
			- Enable client interface lockout: (checked)
		- Uninstallation: Require password to uninstall the client
	- Click-on the Save button
3. [In the Assigned Policies tab] Click-on the Edit Assignment button of the Endpoint Security Common product
	- Server: default
	- Inherit from: Break inheritance and assign the policy and settings below
	- Assigned policy: student10_ENSPolicy (the one you just created)
4. [On client] Open the McAfee Agent Status Monitor
	- Click-on Collect and Send Props
```

### Updating Firewall Rules
Allow Facebook/Youtube, but block MSN.
```bash
1. Click-on System Tree > Select your OU
	- Click-on the Assigned Policies tab
2. Click-on All in the Product drop-down menu > Block & Allow Website List Policy (Policy column)
	- Click-on the Duplicate button
		- Name: student10__Policy_BlockAllowWebsiteList
		- Edit/Add websites as desired
	- Click-on Save
3. [In the Assigned Policies tab] Click-on the Edit Assignment button of the Block and Allow Website List Policy
	- Server: default
	- Inherit from: Break inheritance and assign the policy and settings below
	- Assigned policy: student10_Policy_BlockAllowWebsiteList (the one you just created)
4. [On client] Open the McAfee Agent Status Monitor
	- Click-on Collect and Send Props
5. Click-on Endpoint Security Firewall in the Product drop-down menu > DISA Stig ENS FW Rules Default Policy (Policy column)
	- Click-on the Duplicate button
		- Name: student10_Policy_DISAStigENSFWRulesDefault
		- Edit/Add websites as desired
	- Click-on Save
6. [In the Assigned Policies tab] Click-on the Edit Assignment button of the DISA Stig ENS FW Rules Default Policy
	- Server: default
	- Inherit from: Break inheritance and assign the policy and settings below
	- Assigned policy: student10_Policy_DISAStigENSFWRulesDefault (the one you just created)
7. [On client] Open the McAfee Agent Status Monitor
	- Click-on Collect and Send Props
```

### Accessing WOAC ePO Server for Labs
```bash
# https://hbssepo.train.net:8007

# How to Setup an HBSS Client 
# download Windows 10 VM; not all versions of Windows 10 are supported!

# How to Setup an ePO Server 
# download Windows Server 2012 R2 VM
```
