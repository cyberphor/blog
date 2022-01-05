---
layout: post
title: "PII & How to Protect It"
permalink: 'pii-how-to-protect-it'
category: essays
subcategory: 'vulnerability-management'
---

This blog post is about Personally Identifiable Information (PII) and how to protect it. It is a edited version of the class I gave to the city of El Paso, Texas during a cyber-security seminar on January 12th, 2019. All of the information I gathered here is open source. The National Institute of Standards and Technology (NIST) defines PII as, “…any information about an individual maintained by an agency, including (1) any information that can be used to *distinguish* or *trace* an individual’s identity…and (2) any other information that can be linked or linkable to an individual…”

## Table of Contents
* [What is Linkable Information?](#what-is-linkable-information)
* [Why PII Must Be Protected](#why-pii-must-be-protected)
* [Principles to Follow](#principles-to-follow)
* [PII Impact Levels](#pii-impact-levels)
* [PII Impact Level Considerations](#pii-impact-level-considerations)
* [Privacy Impact Assessments](#privacy-impact-assessments)
* [Operational Safeguards and Privacy-Specific Safeguards](#operational-safeguards-and-privacy-specific-safeguards)
* [Other Safeguards](#other-safeguards)
* [Conclusion](#conclusion)
* [References](#references)

![spreadsheet]({{ site.url }}{{ site.baseurl }}/_assets/PII/spreadsheet.png)
![vin]({{ site.url }}{{ site.baseurl }}/_assets/PII/vin.png)

**Examples of PII**<br>
* Full name, maiden name, alias
* ID number
    * Social Security Number, driver’s license number, bank account number
* Phone number
* Home address
* Email address
* MAC address
* IP address
* Photographic image of your face or other distinguishing features
    * X-rays, fingerprints, retina scans, voice signature, facial geometry
* Information about your personal property
    * Vehicle ID number, title number
* *Linkable information*

## What is Linkable Information?
Most of the time, people who know or understand PII only care to identify what is explicitly PII. They will say, “…only my Social Security Number and Date of Birth are personal information.” Wrong. They’re overlooking what NIST describes as Linkable information. Linkable information are details that can be “logically associated” with an individual. For example, doctors record the blood type of the patients while real estate agents record the neighborhoods they live in. Separately, these two pieces of information cannot precisely distinguish someone from another. Yet, when linked, they can narrow down the number of possibilities as to who it could be.

![gymcard]({{ site.url }}{{ site.baseurl }}/_assets/PII/gymcard.png)
![bizcard]({{ site.url }}{{ site.baseurl }}/_assets/PII/bizcard.png)

**Examples of Linkable PII**<br>
* Date/place of birth
* Race
* Religion
* Weight
* Geographical indicators
* Employment information
* Medical information
* Financial information

## Why PII Must Be Protected
According to the Privacy Act of 1974, organizations are required to follow Fair Information Privacy Practices, otherwise known as FIPPs or Privacy Principles. In short, these principles establish protection, collection, maintenance, and dissemination requirements.

![fipps]({{ site.url }}{{ site.baseurl }}/_assets/PII/fipps.tif)

For those interested in a little history of these principles, the 1974 Privacy Act was passed after the Department of Health, Education, and Welfare (DHEW) filed a report to Congress recommending the adoption of a privacy code. The DHEW made this recommendation in response to the IRS, FBI, and the Nixon Administration’s surveillance activities at the time (think the Watergate scandal).

![coversheet]({{ site.url }}{{ site.baseurl }}/_assets/PII/coversheet.tif)
![nixon]({{ site.url }}{{ site.baseurl }}/_assets/PII/nixon.png)

## Principles to Follow
OK, so there’s more rules to handling PII. What are they? Well, the most widely adopted FIPPs or Privacy Principles are defined by the international Organization for Economic Cooperation and Development (OECD).

**OECD's Privacy Principles**  
* Collection Limitation
* Data Quality
* Purpose Specification
* Use Limitation
* Security Safeguards
* Openness
* Individual Participation
* Accountability

In other words, as an organization, you must:   
* Limit the amount you collect
* Have a legitimate reason to collect
* Make sure it’s accurate
* Keep it confidential
* Be transparent about handling someone’s personal info
    * It should not be a secret to them
* Practice due diligence and due care when handing someone else’s personal info
* Support employee and/or customer rights by:
    * allowing them to confirm whether or not data has been collected on them
    * communicating with them about their data:
         * within a reasonable time
         * without an excessive financial charge, if any
         * in a reasonable manner
         * in a form they can understand
    * giving them a reason if their inquiries are denied
         * allowing them to challenge inquiry denials
    * allowing them to challenge data relating to them
         * erasing, rectifying, completing, or amending data if their challenges are successful

Lastly, your focus as an organization should not just be transparency about handling private information. You should follow these FIPPs or Privacy Principles in an effort to also *prevent* disclosure. You don’t want to be that company that just says, “hey look, we know your Social Security Number.” You want to be the company that says, “hey look, we know your Social Security Number and this is what we’re doing to secure it.”

## PII Impact Levels
How does one follow these FIPPs and start protecting the PII of their employees, users, or customers? Well, they need to first determine the Impact Level of they you believe to be PII. According to NIST, the PII Impact Levels of Low, Moderate, and High represent the “potential harm that could result to the…individuals and/or organization if…it were inappropriately accessed, used, or disclosed.”

This is important as not all Personally Identifiable Information or Linkable PII requires the same level of protection. For example, phone rosters or phone directories are often publicly shared on purpose. Their PII Impact Level could be categorized as “Not Applicable.”

**PII Disclosure Impact Levels**  
* Not applicable
* Low
* Moderate
* High

So don’t get confused with this, PII is still PII. Yet, the things you do to protect it vary depending upon how bad the effect releasing that information would have on you or your customers. This goes back to what I explained earlier with how if you collect a handful of general details about someone they can help distinguish that person from other people. So if the Impact Level of some PII is “Not Applicable,” that means you must invoke the security controls of the “PII Impact Level of Not Applicable.”

What about Low, Moderate, or High Impact? In short, Low Impact would cause, at most, an inconvenience. Like if your cellphone was publicly shared without your permission and then you had to go and get it changed because you didn’t want people to know your actual phone number.

Moderate Impact would cause Identity Theft, embarrassment, or blackmail. One example would be when a company gets hacked and there’s a data breach. The hacker releases your Social Security Number, Date of Birth, and Home Address on the dark web and someone assumes your identity.

![newphone]({{ site.url }}{{ site.baseurl }}/_assets/PII/newphone.png)
![blackmail]({{ site.url }}{{ site.baseurl }}/_assets/PII/blackmail.png)

High Impact would cause loss of life, livelihood, or “inappropriate physical detention.” One clear example of PII that would fall into this category are your allergies. If someone learned what you were allergic to and wanted you dead, they could either lace your food or put something in your drink. Pictured below is a scene from a show called *Elementary*. It’s about a new-age Sherlock Holmes. In this particular episode he’s investigating the death of someone. Come to find out, that person was murdered because someone spiked his food with peanut oil, something the victim was allergic to. Granted, this is a fictional example, but think about how real of a possibility this could be if your personal health information was linked online.

![sherlock]({{ site.url }}{{ site.baseurl }}/_assets/PII/sherlock.png)

## PII Impact Level Considerations
Now, as an organization, you may realize not all of the information you handle falls neatly into one of these Impact Levels. So NIST outlines some factors to you help when determining your PII Impact Levels:

* Identifiability
* Quantity
* Data Field Sensitivity
* Context of Use
* Obligation to Protect
* Access/Location

Identifiability means how easily the data can be used to distinguish someone from another. Again, going back to true or pure PII versus Linkable information, PII may call for a higher Impact Level than Linkable information.  

Quantity refers to the number of individuals or records a database holds that contains PII. For example, a database with 30 records may have a lower Impact Level than another with 100 records. Although, NIST also points out that you should not lower an Impact Level of a PII-handling system simply because it only handles a few records.

Data Field Sensitivity refers to the potency of a single cell of private data. If one cell contains something overtly sensitive, that record may warrant a higher Impact Level than the others.

Speaking of which, a single cell of private data may also warrant a high Impact Level depending upon the context in which it is being handled. For instance, your full name and the city you live in do not call for High or maybe even Moderate Impact Levels. Yet, if you were in Witness Protection, they could.

As for Obligation to Protect, I highly recommend speaking with a Legal Expert or your Legal Department to make sure you’re in compliance with what you’re required to be in accordance with.

Finally, where you maintain data should also be a factor in determining the Impact Level of PII. For example, if PII data gets moved off-site for backup or is accessed by VPN users, it may need to be treated with higher level security controls.

## Privacy Impact Assessments
When the Fair Information Privacy Practices are applied, the end product your organization should produce is known as a Privacy Impact Assessment, or PIA. PIAs are *memorandums for record*, outlining how your organization collects, uses, shares, and maintains personal/private information. There should be one PIA for every unique information system your organization uses to handle PII. Pictured below is an example of what Privacy Impact Assessments looks like. GSA is a provider of office products and communications relating to government. This particular PIA addresses the GSA web site called, "GSAJOBS" and how it handles PII.

![gsajobs]({{ site.url }}{{ site.baseurl }}/_assets/PII/gsajobs.png)

## Operational Safeguards and Privacy-Specific Safeguards
Now, there are two categories of safeguards you could implement in response to your PIAs: operational safeguards and privacy-specific safeguards. Examples of operational safeguards are putting policies and standard operating procedures in place to identify consequences for employees if they do not comply with established guidance on handling PII; Another is facilitating Education, Training, and Awareness events about PII. For example, this keynote can be considered an PII Awareness Event. Yet, your organization’s Privacy Officer should develop and teach ways of integrating the principles and concepts covered today into your actual 9 to 5 job.

As for Privacy-Specific safeguards, you can Minimize Use by only keeping what is absolutely necessary to perform a service, on-hand. You should also routinely review the data you have on-hand to ensure it’s still relevant to the service you are performing.

You can De-Identify information by obscuring or masking it. One example of De-Identifying is hashing. For instance, you could run a phone or identification number through a hashing algorithm like MD5 or SHA256 and then save the value in place of your original data.

![piihashed]({{ site.url }}{{ site.baseurl }}/_assets/PII/piihashed.png)<br>
(Image borrowed from [Net Natives](https://netnatives.com/2017/03/02/why-hashing-should-be-important-to-you/))

Now, to quickly explain what is hashing is: hashing is a one-way cryptographic function. If you feed a hashing algorithm data, it will spit out a fixed-length value of alpha-numeric characters as output. I think a decent metaphor would be shredding a piece of paper and then using the weight of the shredded bits to represent whatever the paper originally had on it. By De-identifying, you’re just making it a little bit harder to deduce what that information is. Although, be careful about using this specific technique for personal health information or PHI.

Often, hackers can defeat hashing by identifying the algorithm and using specific input parameters to predict the output. For example, if I’m a hacker and I’m trying to crack information about your blood. I just need to identify what algorithm was used to generate your blood type’s hash value and then feed it every possible blood type. If it matches one I see in the database, I can deduce your blood type.

Finally, when you Anonymize data you’re essentially destroying it by De-Identifying it and then throwing away the key. Using our previous example, you would essentially hash it and then rename the column that cell represents. This safeguard is typically used in testing environments where data must be randomly generated. You would not use this in a production environment. You would use it when you need to calculate stats about a database.

## Other Safeguards
Now, the safeguards I just mentioned are mostly unique to data relating to privacy such as PII and PHI. You should already have an Information Security program in place as the foundation for how your organization protects its assets. Nonetheless, this is a short list of other safeguards you can implement to protect PII.

* Audit of Events
* Separation of Duties
* Identification and Authentication

Audit of events is logging whenever PII data is accessed. Obviously, preventing unauthorized access is ideal, but you must always be able to detect it. Identification and Authentication involves using strong mechanisms to prove a person claiming to be someone. One example is using two-factor authentication such as requiring something a person can have such as a card and something they know such as a PIN. It also involves automatically logging-out a person if they show signs of inactivity after a while. Separation of Duties enables Role-based Access Control or RBAC. RBAC is when you are allowed to act upon a set of data only if your Role or job function is authorized to access it. For example, Data Entry Technicians may have direct access to a database with privacy data, but as a Sales Person you may not.

## Conclusion
In summary, PII is “(1) any info that can be used to distinguish an individual’s identity, and (2) any other information that can be linked or linkable to an individual.” Because of the Privacy Act of 1974, you must follow Fair Information Privacy Practices which outline your organization’s responsibilities when collecting, using, sharing, and maintaining and PII. To comply with these Principles, you can conduct Privacy Impact Assessments to determine the Impact Levels of the data your systems handle. Once this is identified, you should implement various security controls according to these specific levels. And once again, some of these controls are unique to privacy-type data, but others should already be part of your information security program.

### References
* [NIST SP 800-122 Guide to Protecting Confidentiality of PII](https://csrc.nist.gov/publications/nistpubs/800-122/sp800-122.pdf)
* [U.S. Department of Justice: Overview of the Privacy Act of 1974 - Policy Objectives](https://www.justice.gov/opcl/policy-objectives)
* [Electronic Privacy Information Center: The Privacy Act of 1974](https://epic.org/privacy/1974act/)
* [Organization for Economic Cooperation and Development (OECD): The OECD Privacy Framework](http://www.oecd.org/sti/ieconomy/oecd_privacy_framework.pdf)
* [GSA: Privacy Impact Assessments (PIAs)](https://www.gsa.gov/reference/gsa-privacy-program/privacy-impact-assessments-pia)
* [DHS: The Fair Information Practice Principles](https://www.dhs.gov/xlibrary/assets/privacy/privacy_policyguide_2008-01.pdf)
* [DHS: Privacy Impact Assessment Update for the Advance Passenger Information System](https://www.dhs.gov/sites/default/files/publications/privacy-pia-cbp-apis-update-06052015_0_0.pdf)
* [Medium: Why hashed Personally identifiable information (PII) on the blockchain can be safe](https://medium.com/meeco/why-hashed-personally-identifiable-information-pii-on-the-blockchain-can-be-safe-b842357b9663)
* [Net Natives: Why should you care about Hashing?](https://netnatives.com/2017/03/02/why-hashing-should-be-important-to-you/)
