---
layout: post
title: 'Forensics: Microsoft Windows'
permalink: 'forensics-microsoft-windows'
category: notes
---

## Table of Contents
1. [Image RAM from a live system](#image-ram-from-a-live-system)
2. [Check for disk encryption on a live system](#check-for-disk-encryption-on-a-live-system)
3. [Create a quick triage image on a live system](#create-a-quick-triage-image-on-a-live-system)

## Image RAM from a live system
Using FTK Imager (launched from a USB connected to live system)
```bash
1. Click-on 'File' > 'Capture Memory'
	- Browse: <file-path to USB for RAM acquisition>
	- Check: Include page file
	- Check: Create ADI file (compressed & creates an audit log w/hashes)
2. Click-on 'Capture Memory' # output is a .ad1 file
```
Using DumpIt (launched from a USB connected to live system)
```bash
1. Double-click on DumpIt program
2. Type 'y'
# image of RAM is saved w/YYYY-MM-DD-HH-MM-SS (UTC timestamp) as filename on USB
```

## Check for disk encryption on a live system
Using Encrypted Disk Detector (launched from a USB connected to live system)
```bash
edd.exe /accepteula # only checks TrueCrypt, PGP, BitLocker, SafeBoot, Checkpoint, Sophos, and Symantec; if encrypted, create a live Logical image w/FTK Imager next *
```

## Create a quick triage image on a live system
Create a custom triage image using FTK Imager (launched from a USB connected to live system)
```bash
# FTK Imager doesn’t truly mount a device, so it ignores NTFS (does things at the physical level, not through the kernel). Use Arsenal Image Mounter to access Volume Shadow Copies and other NTFS-required nuances (BitLocker).
0. Click-on 'File' > 'Add Evidence Item...'
	* Select 'Logical Drive' and the available drive # ex: 'C:\ - [NTFS]'
1. Right-click on artifact/folder of interest in Evidence Tree window
	- Select 'Add to Custom Content Image (AD1)'
2. [Optional] In the 'Custom Content Sources' tab, click-on 'New'
	- Select '*' and click-on 'Edit' # the '*' populates after the previous step
	- Type desired search string # good to pick .dlls and .sys to scan for hacker files
		- Check: Ignore case, Include Subdirectories, Match All Occurrences
3. Click-on 'Create Image'
	- Click-on 'Image Destination | Add' > fill-in triage image info
	- Browse to 'Image Destination Folder'
	- Specify image filename # ex: Case01_Laptop_TriageImage_20190708
	- Specify image fragment size # ex: 1024 MB; if img > 1 GB = fragments
	- Click-on 'Finish' and 'Start' # output is a .ad1 file
```

Grab Registry Core/User hives using FTK Imager (launched from a USB connected to live system)
```bash
0. Click-on 'File' > 'Add Evidence Item...'
1. Click-on 'Obtain Protected Files' # cube icon in top navigation bar
	- Specify destination to save obtained files
	- Select the 'Password recovery and all registry files' option
	- Click-on 'OK' # output are Registry files
```

Create a custom triage image using CyLR 
```bash
# specify custom artifact list; save output to USB (D: drive; result is a .zip)
CyLR.exe -c customArtifactList.txt -o "D:\evidence\hostname_of_suspect_PC”

# send artifacts to SFTP server and keep artifacts in memory
cyLR.exe -u <username> -p <password> -s <sftp server IP addr> -m
```
