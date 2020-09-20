---
layout: post
title: 'ACAS: Practical Exercises'
permalink: 'acas-practical-exercises'
category: notes
subcategory: exercises
---

**Hand-on Practical Exercise #1**
1. Download Windows 10 VM
2. Install Wireshark
3. Install SCAP Compliance Checker (SCC)
4. Run local SCC scan (ends by presenting a URL to the view scan results)
5. "STIG" the VM as much as possible (?)
6. Login to schoolhouse ACAS server (https://acas.train.net; student10; 1qazxsw2!QAZXSW@)
7. Review date/time of last plugin feed update
	- Click-on your username > Plugins > Look at Modified column
8. Create a scan
	- Create a plan first; specify the following (in order):
		- Credentials: Windows: Password (use VM username/password)
		- File (if applicable)
		- Policy: Basic Network Scan (use default options for this demo)
		- Scan: Active Scan
			- Policy: the one created
			- Import Repository (under Settings): Classroom101
			- Targets: VM IP
9. Setup Wireshark to watch the scan
	- `ip.addr==10. 120.2.40 and ip.addr==10.25.5.72`
10. Disable firewall of VM (below uses the relevant PowerShell Cmdlet)
	- `Set-NetFirewallProfile -Profile domain,private,public -Enabled false`
11. Start the "Remote Registry" service in the VM
	- `<cmd to start the service>`
12. Start the scan (watch via Wireshark within the VM)
13. Disable "User Account Control (UAC)" by using the Control Panel or by adding a Registry key
	- Create the following DWORD with a value set to 1
	- HKEY_LOCAL_MACHINE = HKLM
	- `New-Item -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\ -Name LocalAccountTokenFilterPolicy -Force`
	- `Set-Item -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\LocalAccountTokenFilterPolicy -Value “1”`
14. Re-enable the firewall (using the same PowerShell Cmdlet)
	- `Set-NetFirewallProfile -Profile domain,private,public -Enabled True`
15. Create a new Audit File, Policy, and Scan
	- Audit File: Windows > DISA Windows 10 STIG v1r14
	- Policy: SCAP and OVAL Auditing
		- Compliance: Use the Audit File you uploaded
	- Scan: Active Scan
		- Policy: use the new policy you created
		- Import Repository (under Settings): Classroom101
		- Targets: VM IP
		- Credentials: Windows: the one you created
16. Update your firewall to allow the ACAS server IP address 
	- `New-NetFirewallRule -DisplayName AllowACASInbound -Direction Inbound -LocalAddress 192.168.56.6 -RemoteAddress 192.168.56.7 -Profile Domain,Private,Public -Protocol Any -LocalPort Any -Action Allow -Enabled True`
	- `New-NetFirewallRule -DisplayName AllowACASOutbound -Direction Outbound -LocalAddress 192.168.56.7 -RemoteAddress 192.168.56.6 -Profile Domain,Private,Public -Protocol Any -LocalPort Any -Action Allow -Enabled True`
17. Run the SCAP and OVAL Auditing scan you just created 
18. Export the results
	- Options > Export as CSV
19. [Optional step] Install Java (for DISA STIG Viewer)
20. Modify your Audit File
	- [File] Scans > Audit Files > Add > Advanced > Audit File > Select "U_MS_Windows_10_V1R14_STIG_SCAP_1-2_Benchmark.zip"
		- Download different benchmarks from DISA (ex: Google Chrome benchmark) and import them as Audit Files
	- [Policy] Scans > Policies > Add > SCAP and OVAL Auditing (Compliance: SCAP Windows > NewAuditFile)
	- [Scan] Scans > Active Scans > Add > (Policy: NewAuditPolicy, Targets: VM, Credentials: Windows, )
21.  Run, export results, and import into the DISA STIG Viewer
	- [ACAS menu] Scans > Scan Results > Gear icon > Download SCAP XML > Extract All	
	- [DISA STIG Viewer: STIG Explorer tab] Import the "MS Windows 10 v1r14 STIIG" file 
	- [DISA STIG Viewer: STIG navigation bar] Checklist > Create Checklist - Check Marked STIG(s)
		- Import > XCCDF File > Browse to extracted scan results folder

STIG-related Resources
- DISA STIG Viewer: https://public.cyber.mil/stigs/stig-viewing-tools/
- LGPO (download the LGPO.zip): https://www.microsoft.com/en-us/download/details.aspx?id=53319
	- Converts GPOs to local policies (ADMX/ADML files)
		- Convert GPOs to the ADMX/ADML file format 
			- `LGPO.exe /g ../"DOD Google Chrome v1r15"`
		- Then, copy them into C:\Windows\PolicyDefintions\
			- This provides the setting requirements specified by DISA STIGs
				- ex: Google Chrome will 
- Link to STIG GPO: https://dl.cyber.mil/stigs/zip/U_STIG_GPO_Package_April_2019.zip

How to enable the Windows Firewall dropped packets log
- Control Panel > Windows Defender Firewall > Advanced settings > Windows Defender Firewall Properties
	- Domain, Private, Public Profile: Logging > Customize... 
		- Log dropped packets: Yes

**Hands-on Practical Exercise #2**
```
# Setup
# Ensure you can ping the Windows 10 client (add firewall rule if necessary)
# Ensure the User Account Control (UAC) is disabled via a special Registry key
# Ensure the REMOTE REGISTRY service is ENABLED

# Create Credentials
Scans > Credentials > Add > Windows: Password
	- Name: Credential_Windows10_Client
	- Password Credential
		- Username: victor
		- Password: <password>

# Upload an Audit File
## You can download benchmarks/STIGs from...
Scans > Audit Files > Add > Custom
	- Name: AuditFile_DISA_Windows10_STIG_v1r14_Benchmark

# Create a Policy
Scans > Policies > Add > SCAP and OVAL Auditing
	- Name: Policy_DISA_Windows10_STIG_v1r14
	- Compliance: Use the Audit File you uploaded
		- Type: Windows
		- Audit File: DISA_Windows10_STIG_v1r14_auditFile

# Create and start an Active Scan
Scans > Active Scans > Add > 
		- Name ActiveScan_DISA_Windows10_v1r14
		- Policy: Policy_DISA_Windows10_STIG_v1r14 (the policy you created)
		- Import Repository: repo01
		- Targets: 192.168.56.7 (Windows 10 VM IP address)
		- Credentials: Credential_Windows10_Client (the one you created)

# Upload offline plugins (you're using a Stand-Alone system)
# You can offline plugins from...
- Login to SecurityCenter as "admin"
- System > Configuration > Plugins/Feed
	- Expand on the SecurityCenter Feed and upload the SecurityCenter file (.tar.gz file)
		- Click-on Submit
	- Expand on the Active Plugins and upload the relevant files (.tar.gz files)
		- Click-on Submit
- Restart your Nessus daemon
	- service nessud restart
	- Login to the Nessus scanner (you should see it compiling your new plugins)

 Perform a scan
```
