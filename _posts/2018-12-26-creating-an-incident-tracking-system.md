---
layout: post
title: 'Creating an Incident Tracking System'
permalink: 'creating-an-incident-tracking-system'
category: posts
subcategory: 'incident-response'
---

## Table of Contents
* [The Phases of Incident Response](#the-phases-of-incident-response)
* [How to Create a Security Incident Tracking System](#how-to-create-a-security-incident-tracking-system)
* [References](#references)

Thanks for returning to read the last part of my three-post series called, *A Wiki for Your SOCs*. In this final blog post, I will explain the different phases of Incident Response (IR) and outline how to create an Incident Tracker using the web portal we previously established. Once again, my intent with this series is to demonstrate how Security Operations Center (SOC) personnel can use free & open-source software to track issues, share documents, and advertise information.

## The Phases of Incident Response
Incident Reponse generally involves six phases: (1) preparation, (2) identification, (3) containment, (4) eradication, (5) recovery, and (6) improvement. Preparing for a security incident includes conducting risk assessments, installing countermeasures, training Incident Response personnel, and ensuring everyone in the organization understands their responsibilities when an incident occurs. It should be obvious to them who to contact, what information to gather about an event, and what should or should not be done (Do you want them to unplug the network cable? How wide must an area be cordoned off?). 

The Identification phase involves verifying whether or not an *incident* actually occured as opposed to a benign system/network event. Not to mention, instead of guessing or spectulating, these two defintions must be spelled-out in your Incident Response policy and standard operating procedures. This phase also involves determining the incident's scope. For example, it is important to be able to answer how many computers were infected or how much intellectual property was siphoned. The answers to these questions directly influence the steps your Incident Response personnel will take during the Containment phase. 

While attempting to *contain* an incident, IR personnel may place infected systems in a secluded, Virtual Local Area Network (VLAN) or use firebreaks (lanes of dirt) to stop a fire from spreading. The Eradication phase involves identifying the *root cause* of an incident and then, developing methods for halting and preventing the violation from occuring again. For instance, a recently exploited vulnerability that allows a buffer overflow can be negated by using input validation prior to accepting user supplied data. In practice, the compromise (root cause; vulnerability) in one or more systems would be identified first. Then, an input validation mechanism would be installed on all relevant systems to mitigate it from happening again or in this manner. 

The Recovery phase is designated for restoring an organization & it's systems to their original security baselines. For instance, if a data center was flooded, the triggered alarms and damaged equipment would be reset and replaced. Another, more common, example is re-imaging a computer with malware. Its contents are written-over a number of times and then, re-populated with trusted software/firmware. 

Lastly, the Improvement phase involves performing a review of what happened, evaluating how well the incident was *handled*, determining how much the incident cost, and bolstering defenses to not only prevent it from happening again, but implementing considerations for similar attacks in the future. Most importantly, it is crucial for organizations to establish and standardize their own incident handling process. By doing so, they will be able to replicate, if not improve on, the quality of response each time an incident occurs as well as minimize the impact to their business or service. 

## How to Create a Security Incident Tracking System
Using the SOC web portal we previously created for the fictional company *BlunderMuffin*, I will now outline how to create an Incident Tracker. We need to first (1) enable a few necessary features in our Tiki Wiki app, (2) create the tracker "shell", and then, (3) add the unique fields that will be the skeleton of our tracker. 

**How to Enable the Trackers Feature**
1. Click-on *Settings* > *Control Panels*
2. Click-on **Features**
3. Under *Main Features*, click-on the *Trackers* check-box
4. Click-on **Apply**

**How to Enable the Auto-Increment Field for Trackers**
1. Click-on *Settings* > *Control Panels*
2. Click-on **Features**
3. Click-on *Trackers* (the database, or stack of pancakes, icon)
4. Toggle the view (top left) from *Basic* to *Advanced*
	* Under the *Field types* tab, click-on the Auto-Increment check-box
	* Click-on **Apply**

**How to Enable the Location Field for Trackers**
1. Click-on *Settings* > *Control Panels*
2. Click-on **Maps**
3. Toggle the view (top left) from *Basic* to *Advanced*
	* Under *Settings*, click-on the *Maps & Location Enabled* check-box
	* Click-on **Apply**

**How to Create a Tracker**
1. Click-on *Trackers* > *List Trackers*
2. Click-on **Create**
	* Name: `Security Incidents`
	* Description: `For Tier 2 Incident Response`
	* Under the *Features* drop-down:
		* Click-on *Allow comments*
	* Click-on **Save**

**How to Add Fields to a Tracker**
1. Click-on *Trackers* > *List Trackers*
2. Click-on <font color='blue'>wrench</font> icon of the *Security Incidents* tracker
	* Click-on *Fields*
3. Click-on **Add Field**
	* Name: <font color='red'>(see table below)</font>
	* Type: <font color='red'>(see table below)</font>
	* Click-on *Add Field & Edit Advanced Options*
		* <font color='red'>Specify options using the table below (or as you see fit)</font>
4. Click-on **Save**

**How to Find Group IDs**<br>
In the table below, I reference ID numbers of the groups I created in the previous blog post called "Using RBAC to Organize a SOC Web Portal" (the groups in question are First responders, Incident handlers, Forensic examiners, and Malware analysts). Use the following steps to see which ID numbers you need to use (keep in mind you want to restrict access to your Incident Tracker to these specific groups). 
1. Click-on *Settings* > *Groups*
2. Under the *List* tab, write-down the *ID* column for each group you want to be able to access your tracker

**Example Fields to Add to a Security Incidents Tracker**

|Name|Type|Options|
|---|---|---|
|Case ID|Auto-Increment|Prepend: `caseID_`|
|Incident Handler|User Selector|Mulitiple selection: `No`<br>Group IDs: `5|6|7|8`|
|Category|Dropdown|Option:<br>`CAT 0 (Exercise),`<br>`CAT 1 (Intrusion),`<br>`CAT 2 (DoS),`<br>`CAT 3 (Malware),`<br>`CAT 4 (Policy Violation),`<br>`CAT 5 (Scan),`<br>`CAT 6 (Unconfirmed)`|
|Date/Time|Date and Time|Default value:<br>`Item creation date and time`|
|Source IP Address|Text Field|Validation - Type:<br>`Regular Expression (Pattern)`<br>Validation - Parameters:<br>`^(([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])$`<br>Validation - Error Message:<br>`Please provide a valid IP address.`|
|Source Port|Numeric Field|Validation - Type:<br>`Regular Expression (Pattern)`<br>Validation - Parameters:<br>`^()([1-9]|[1-5]?[0-9]{2,4}|6[1-4][0-9]{3}|65[1-4][0-9]{2}|655[1-2][0-9]|6553[1-5])$`<br>Validation - Error Message:<br>`Please provide a valid Well-known, Registered, or Dynamic port.`|
|Source Protocol|Dropdown|Option:<br>`ICMP,`<br>`UDP,`<br>`TCP,`<br>`DNS,`<br>`HTTP,`<br>`SMTP,`<br>`Other`|
|Destination IP Address|Text Field|Validation - Type:<br>`Regular Expression (Pattern)`<br>Validation - Parameters:<br>`^(([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])$`<br>Validation - Error Message:<br>`Please provide a valid IP address.`|
|Destination Port|Numeric Field|Validation - Type:<br>`Regular Expression (Pattern)`<br>Validation - Parameters:<br>`^()([1-9]|[1-5]?[0-9]{2,4}|6[1-4][0-9]{3}|65[1-4][0-9]{2}|655[1-2][0-9]|6553[1-5])$`<br>Validation - Error Message:<br>`Please provide a valid Well-known, Registered, or Dynamic port.`|
|Destination Protocol|Dropdown|Option:<br>`ICMP,`<br>`UDP,`<br>`TCP,`<br>`DNS,`<br>`HTTP,`<br>`SMTP,`<br>`Other`|
|OS Information|Dropdown|Option:<br>`Microsoft Windows 10,`<br>`Apple macOS,`<br>`Linux`|
|System Function|Dropdown|Option:<br>`Workstation,`<br>`Server,`<br>`Network device,`<br>`Other`|
|Location|Location||
|Identification Method|Dropdown|Option:<br>`IDS,`<br>`Log analysis,`<br>`Administrator,`<br>`Threat Hunting Section,`|
|Resolution|Text Area|Description:<br>`Ultimate fix-action used to eradicate the root cause`|
|Status|Dropdown|Description:<br>`Current phase of the Incident Response process`<br>Option:<br>`Identifying (confirming the incident),`<br>`Containing (segregating the infected),`<br>`Eradicating (developing countermeasures),`<br>`Recovering (restoring security postures),`<br>`Reviewing (implementing Lessons Learned),`<br>`Closed`|

With the tracker created and structured as desired, let's (1) add a few items to it, (2) experiment with Tracker views, (3) embed the tracker into our Tier 2 Section page, and (4) secure our tracker using the RBAC/AGDLP method demonstrated in the previous blog post. 

**How to Add Security Events/Incidents to Your Tracker**
1. Click-on *Trackers* > *List Trackers*
2. Click-on the name of your tracker
3. Click-on **Create Item**
	* Add information as desired
	* Click-on **Create**

**How to Set the Default View of a Tracker**
1. Click-on *Trackers* > *List Trackers*
2. Click-on the <font color='blue'>wrench</font> icon of your tracker
	* Click-on *Fields*
3. Under the *List* column, click-on each *Field's* check-box you want to see displayed in your default view
	* For example, I have these checked:
		* Case ID
		* Incident Handler
		* Category
		* Date/Time
		* Status
4. Under the *Mandatory* column, click-on each *Field's* check-box you want to be required when someone adds an item
	* For example, I have these checked to be *Mandatory*:
		* Case ID
		* Incident Handler
		* Category
		* Date/Time
		* System Function
		* Status
4. Select *Save All* in the drop-box at the bottom of the page
	* Click-on **Go**  

**How to Embed a Tracker into a Page**
1. On your desired page, click-on **Edit**
2. Copy & paste the code below into the provided text field: 
```
{TABS("Create a Security Incident Ticket|View Open Tickets")}
{TRACKER(trackerId="1" action="Save")}Your ticket has been created. Please click-on the "View Open Tickets" tab.{TRACKER}
/////
{trackerlist(trackerId=>1, fields=>1:2:3:4:15, showfieldname=>y, sort_mode=>f_4_dsec, showlinks=>y)}
{TABS}
```
*This snippet of code creates two tabs on your desired page: one for creating tickets, the other for reviewing tickets.*
3. Click-on **Save**

**How to Secure a Tracker Using Groups**<br>
In the previous blog post, we updated the minimum requirement to access our Tier 2 Section page as "being a member of the Tier 2 Section group." Yet, our tracker has additional features we must ensure only authorized personnel can exercise. Follow the steps below to lock-down these features. 

1. Click-on *Trackers* > *List Trackers*
2. Click-on the <font color='blue'>wrench</font> icon of your tracker
	* Click-on **Permissions**
3. Click-on the *Select groups* tab
	1. De-select the check-box for the *Anonymous* group
		* <font color='red'>The <i>Anonymous</i> group represents public access. If you leave this checked, you could accidentally grant <b>everyone</b> read/write access to the page.</font>
	2. Click-on each group's check-box you want to manage access for
		* Example groups who may need access to your *Security Incidents* tracker
			* Tier 2 Section Create
			* Tier 2 Section Read
			* Tier 2 Section Update
			* Tier 2 Section Delete
	3. Click-on **Select**
4. Click-on each check-box of the permissions you want to grant
	* Example permissions to grant the groups identified above for your *Security Incidents* tracker
		* Tier 2 Section Create<br>
			* Can post new comments (*tiki_p_post_comments*)
			* Can attach files to tracker items (*tiki_p_attach_trackers*)
			* Can post tracker item comments (*tiki_p_comment_tracker_items*)
			* Can create new tracker items (*tiki_p_tracker_items*)
			* Can export tracker items (*tiki_p_export_items*)
		* Tier 2 Section Read
			* Can read comments (*tiki_p_read_comments*)
			* Can view tracker item attachments and download them (*tiki_p_tracker_view_attachments*)
			* Can view tracker item comments (*tiki_p_tracker_view_comments*)
			* Can view trackers (*tiki_p_view_trackers*)
			* Can view closed tracker items (*tiki_p_view_tracker_items_closed*)
			* Can view pending tracker items (*tiki_p_view_tracker_items_pending*)
			* Can watch a tracker (*tiki_p_watch_trackers*)
		* Tier 2 Section Update
			* Can edit all comments (*tiki_p_edit_comments*)
			* Can change tracker items (*tiki_p_modify_tracker_items*)
			* Can change pending tracker items (*tiki_p_modify_tracker_items_pending*)
			* Can change closed tracker items (*tiki_p_modify_tracker_items_closed*)
		* Tier 2 Section Delete
			* Can delete comments (*tiki_p_remove_comments*)
			* Can remove tracker items (*tiki_p_remove_tracker_items*)
			* Can remove pending tracker items (*tiki_p_remove_tracker_items_pending*)
			* Can remove closed tracker items (*tiki_p_remove_tracker_items_closed*)
	* To easily grant all permissions to a group, click-on the group's check-box at the very top
		* Your *Web administrators* group may need full control for example
5. Click-on **Assign**
	* Click-on **Confirm action**

As before, if anyone outside of the Tier 2 Section needed access, you would simply configure their *Global* group to inherit from the *Domain Local* group that has the permissions desired. For example, to allow `michael_scott` (a member of the SOC Leaders Global group), Read-only access (a Tier 2 Section Read permission)  to entries on your Security Incidents tracker, you would perform the following steps. 

1. Click-on *Settings* > *Groups*
2. Click-on the <font color='blue'>wrench</font> icon of the *SOC leaders* group
	* Click-on *Edit*
3. Under the *Edit SOC leaders* tab, select/highlight *Tier 2 Section Read* in addition to the groups previous selections (*Registered*)
4. Click-on **Save**
	* Click-on **Modify** to confirm

Now, the user `michael_scott` has the ability to read comments and view items/attachments within the *Security Incidents* tracker (thanks to the RBAC/ADGLP permission configuration we set-up above).   

### References
* [BMC: ITIL Incident Management](http://www.bmc.com/guides/itil-incident-management.html)
* [BestPractical: Request Tracker for Incident Response](https://bestpractical.com/rtir/)
* [US-CERT: Incident Reporting Requirements](https://www.us-cert.gov/government-users/reporting-requirements)
* [Tiki: Viewing Tracker Results](http://doc.tiki.org/Viewing-Tracker-Results)
* [elastic: Using Nmap + Logstash to Gain Insight Into Your Network](https://www.elastic.co/blog/using-nmap-logstash-to-gain-insight-into-your-network)
* [Offensive ELK: Elasticsearch for Offensive Security](https://www.marcolancini.it/2018/blog-elk-for-nmap/)
* [Tiki: How to administrate Tiki from inside Tiki, when install is complete and correct](https://tiki.org/tiki-view_faq.php?faqId=3#q94)
* [EY: Security Operations Centers — helping you get ahead
of cybercrime](https://www.ey.com/Publication/vwLUAssets/EY-security-operations-centers-helping-you-get-ahead-of-cybercrime/$FILE/EY-security-operations-centers-helping-you-get-ahead-of-cybercrime.pdf)
* [IBM Security: Virtual Security Operations Center (VSOC) Portal User Guide](https://portal.sec.ibm.com/mss/html/en_US/eLearning_Courses/pdf/vsoc_portal_user_guide.pdf)
* [WebsiteForStudents: Setup Tiki Wiki Groupware CMS On Ubuntu 16.04](https://websiteforstudents.com/setup-tiki-wiki-groupware-cms-on-ubuntu-16-04-17-10-18-04-with-nginx-mariadb-and-php-7-1-support/)
* [RegEx Pal: TCP/UDP Port, range 1-65535](https://www.regexpal.com/104146)
* [NIST SP 800-61 Rev2](https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-61r2.pdf)
* [EC-Council: Computer Forensics - Investigation Procedures and Response (CHFI), 2nd Edition](https://online.vitalsource.com/#/books/9781337512893/)
* [EC-Council: Computer Forensics - Investigating Network Intrusions and Cybercrime (CHFI), 2nd Edition](https://online.vitalsource.com/#/books/9781337015707/)
* [ACP Ethical Hacking and Countermeasures & Computer Forensics, 1st Edition](https://online.vitalsource.com/#/books/9781337555876/)
* [Tiki: Plugin Tracker List](https://doc.tiki.org/PluginTrackerList)
