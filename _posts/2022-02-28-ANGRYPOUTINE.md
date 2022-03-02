---
layout: post
title: 'ANGRYPOUTINE'
permalink: 'angrypoutine'
---
**Download PCAP**  
[https://www.malware-traffic-analysis.net/2021/09/10/2021-09-10-traffic-analysis-exercise.pcap.zip](https://www.malware-traffic-analysis.net/2021/09/10/2021-09-10-traffic-analysis-exercise.pcap.zip)

**Executive Summary**  
On  2021-09-10, at approximately  23:17:27 UTC, an OPERATING_SYSTEM host used by FIRST_NAME LAST_NAME was infected with Bazar malware.

**Victim Details**  
* Username: TBD (frame #???)
* Hostname: TBD (frame #???)
* IP Address: 10.9.10.102 (frame #89)
* MAC Address: 00:4f:49:b1:e8:c3 (frame #89)
* Serial Number: n/a

**Indicators of Compromise**  
* Network
  * 10.9.10.102, 194.62.42.206, GET, “/date1?BNLv65=pAAS”
* Files
  * SHA-256 Hash: eed363fc4af7a9070d69340592dcab7c78db4f90710357de29e3b624aa957cf8
  * File Size: 279 Kilobytes
  * File Location: /bmdff/BhoHsCtZ/MLdmpfjaX/5uFG3Dz7yt/date1?BNLv65=pAAS
  * File Type: PE32+ executable (DLL) (GUI) x86-64, for MS Windows
  * File Description: Windows DLL

**Notes**  
Elaborate on the alerts that were raised, lessons learned, recommendations, etc. 

**Run the PCAP through the Suricata IDS.**  
```
mkdir suricata
cd suricata
suricata -r ../traffic.pcap
```

**What was the total number of alerts raised?**  
```
cat fast.log | wc -l
```
38

**How many unique alerts were raised?**  
```
cat fast.log | awk -F '\\[\\*\\*\\] ' '{print $2}' | sort | uniq
[1:2014520:7] ET INFO EXE - Served Attached HTTP 
[1:2018959:4] ET POLICY PE EXE or DLL Windows file download HTTP 
[1:2028765:2] ET JA3 Hash - [Abuse.ch] Possible Dridex 
[1:2033385:1] ET POLICY IP Check Domain (myexternalip .com in TLS SNI) 
[1:2260002:1] SURICATA Applayer Detect protocol only one direction
```
5

**How many times was each unique alert raised?**  
```
cat suricata/fast.log | awk -F '\\[\\*\\*\\]' '{print $2}' | sort | uniq -c | sort -r
     34  [1:2028765:2] ET JA3 Hash - [Abuse.ch] Possible Dridex 
      1  [1:2260002:1] SURICATA Applayer Detect protocol only one direction 
      1  [1:2033385:1] ET POLICY IP Check Domain (myexternalip .com in TLS SNI) 
      1  [1:2018959:4] ET POLICY PE EXE or DLL Windows file download HTTP 
      1  [1:2014520:7] ET INFO EXE - Served Attached HTTP
```

**Based on the summary of alerts, what do you think happened?**  
Someone downloaded and executed Dridex malware after browsing to a malicious website.

**What are the first three unique alerts raised?**  
```
cat fast.log | head -n3
09/10/2021-19:17:32.338290  [**] [1:2028765:2] ET JA3 Hash - [Abuse.ch] Possible Dridex [**] [Classification: Unknown Traffic] [Priority: 3] {TCP} 10.9.10.102:58132 -> 167.172.37.9:443
09/10/2021-19:17:29.191744  [**] [1:2018959:4] ET POLICY PE EXE or DLL Windows file download HTTP [**] [Classification: Potential Corporate Privacy Violation] [Priority: 1] {TCP} 194.62.42.206:80 -> 10.9.10.102:58131
09/10/2021-19:17:29.191744  [**] [1:2014520:7] ET INFO EXE - Served Attached HTTP [**] [Classification: Misc activity] [Priority: 3] {TCP} 194.62.42.206:80 -> 10.9.10.102:58131
```

**What is the rule criteria for the first alert raised?**  
```
sudo cat /var/lib/suricata/rules/suricata.rules | grep 2028765
alert tls $HOME_NET any -> $EXTERNAL_NET any (msg:"ET JA3 Hash - [Abuse.ch] Possible Dridex"; ja3_hash; content:"51c64c77e60f3980eea90869b68c58a8"; reference:url,sslbl.abuse.ch/ja3-fingerprints/; classtype:unknown; sid:2028765; rev:2; metadata:created_at 2019_10_14, former_category JA3, updated_at 2019_10_29;)
```
The first rule looks for the JA3 hash “51c64c77e60f3980eea90869b68c58a8.”

**Do any packets match the criteria of the alert raised?**  
```
tshark -r traffic.pcap -Y "ja3.hash == 51c64c77e60f3980eea90869b68c58a8" -T fields -e frame.number -e ip.src -e ip.dst -e ja3.hash
1487	10.9.10.102	167.172.37.9	51c64c77e60f3980eea90869b68c58a8
1845	10.9.10.102	94.158.245.52	51c64c77e60f3980eea90869b68c58a8
1863	10.9.10.102	94.158.245.52	51c64c77e60f3980eea90869b68c58a8
# …snipped…
```
Yes. A total of 34 frames match the rule’s criteria. 

**Do any of the packets in question (before and during the activity that triggered alerts) contain domain names and if so, were there name resolution requests for these domain names? If not, how did the victim learn about the attacker?**
```
tshark -r traffic.pcap -Y "ip.addr == 167.172.37.9"
tshark -r traffic.pcap -Y "ip.addr == 94.158.245.52" 
tshark -r traffic.pcap -Y "frame contains londonareloeli.uk"
```
Yes. Frame #1489 is one of the first “TLS 1.2 Server Hello” packets. It contains the domain name “londonareloeli.uk.” Yet, there are no name resolution requests for it within the PCAP and VirusTotal also reports it as benign. 

**What is the victim’s username and hostname?**  

**What files were downloaded via HTTP?**  
```
tshark -r traffic.pcap -q --export-objects http,http
ls http | wc -l
```
Yes. 46 files were downloaded via HTTP.

**What were the types of files downloaded?**  
```
for i in $(ls -d http/*); do file "$i" | cut -d' ' -f2; done | sort | uniq -c | sort -rn
     33 Microsoft
      5 PNG
      3 ASCII
      2 PE32+
      2 data
      1 JPEG
```

**Are any of the executable binary files malicious?**    
```
sha256sum http/date1%3fBNLv65\=pAAS
eed363fc4af7a9070d69340592dcab7c78db4f90710357de29e3b624aa957cf8
```
Yes, 52 of 69 antivirus vendors report the file with the SHA256 hash “eed363fc4af7a9070d69340592dcab7c78db4f90710357de29e3b624aa957cf8” as malicious.  
![VirusTotal]({{ site.url }}{{ site.baseurl }}/_assets/2022-02-28-ANGRYPOUTINE.png)

**In what packet was this malware requested?**  
```
89 2021-09-10 23:17:27.822861  10.9.10.102 → 194.62.42.206 HTTP 410  GET /bmdff/BhoHsCtZ/MLdmpfjaX/5uFG3Dz7yt/date1?BNLv65=pAAS HTTP/1.1 
```
Frame #89 in the PCAP contains a HTTP GET request for “/date1?BNLv65=pAAS.”

**How did the victim learn about the malware server?**  
```
tshark -r traffic.pcap -Y "dns.a == 194.62.42.206"
 1182 139.466514    10.9.10.9 → 10.9.10.102  DNS 95 194.62.42.206 Standard query response 0x9e41 A simpsonsavingss.com A 194.62.42.206
```
The victim learned about the malware server after requesting a name resolution for the domain “simpsonsavingss.com.” 

**Were there any other packets containing name resolution requests for the domain in question?**  
```
tshark -r traffic.pcap -Y "dns.qry.name == simpsonsavingss.com"
 1180 139.233028  10.9.10.102 → 10.9.10.9    DNS 79  Standard query 0x9e41 A simpsonsavingss.com
 1181 139.313234  10.9.10.102 → 10.9.10.9    DNS 79  Standard query 0x9e41 A simpsonsavingss.com
 1182 139.466514    10.9.10.9 → 10.9.10.102  DNS 95 194.62.42.206 Standard query response 0x9e41 A simpsonsavingss.com A 194.62.42.206
```
Yes. A total of three packets contained a name resolution request for this specific domain.

**Were the files executed?**  


