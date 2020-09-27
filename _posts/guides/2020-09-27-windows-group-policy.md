---
layout: post
title: 'Windows Group Policy'
permalink: 'windows-group-policy'
category: notes
subcategory: guides
---

### Using Startup Scripts to Deploy Software
1. Open the "Group Policy Management" snap-in
2. Right-click "Group Policy Objects" and select "New"
3. Name your GPO when prompted
4. Right-click your newly created GPO and select "Edit..."
5. Under "Computer Configuration," click-on "Polices > Windows Settings > Scripts (Startup/Shutdown)"
6. Double-click "Startup" 
7. Click-on the "PowerShell Scripts" tab
8. Click-on "Show Files..."
9. Copy & paste your script and any files it requires to this window
10. Click-on "Add..." and browse to where you uploaded your script
11. Highlight your script and click-on "Open" 
12. Specify any script parameters and then, click-on "OK"
  * Don't forget to link your existing GPO to the relevant OU
  * Also, rememeber to run `gpupdate /force; shutdown /r /t 000` when testing your startup script
