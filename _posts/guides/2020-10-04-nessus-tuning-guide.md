---
layout: post
title: 'Nessus: Tuning Guide'
permalink: 'nessus-tuning-guide'
category: notes
subcategory: guides
---

### Table of Contents
* [Updating Audit Files](#updating-audit-files)

### Updating Audit Files
Audit Files are security baselines you want to measure a machine against. 
1. Download the .xml or .audit file from [DISA](https://public.cyber.mil/stigs/), [NVD](https://nvd.nist.gov/ncp/repository), [CIS](https://www.cisecurity.org/cis-benchmarks/), [Tenable](https://www.tenable.com/downloads/audit)
2. Unzip the .xml or .audit file
3. Click-on "Scans > Audit Files"
4. Click-on "Add an Audit File" or "+ Add" (top-right)
5. Select "Advanced"
6. Provide the following details and click-on "Submit"
    - Name: `DISA Microsoft Windows 2012 and 2012 R2 DC STIG v2r19`
    - Description: `A tool to improve the security of Department of Defense (DoD) information systems.`
    - Audit File: ` U_MS_Windows_2012_and_2012_R2_DC_V2R19_STIG_SCAP_1-2_Benchmark.xml`

### References
* [DISA STIGs Document Library](https://public.cyber.mil/stigs/)
* [NIST National Vulnerabiltiy Database](https://nvd.nist.gov/ncp/repository)
* [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks/)
* [Compliance & Audit Files](https://www.tenable.com/downloads/audit)
* [Tenable.sc - "Add a Custom Audit File"](https://docs.tenable.com/tenablesc/Content/AddCustomAuditFile.htm)
* [Tenable Community - "How to upload Custom STIG file in Nessus Professional"](https://community.tenable.com/s/feed/0D53a00006WYfAwCAL)
