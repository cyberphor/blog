---
layout: post
title: 'Hosting a Mini SIEM'
category: essays
subcategory: 'network-security-monitoring'
permalink: 'hosting-a-mini-siem'
---

Security Incident & Event Management Systems (SIEMs) are used by analysts to quickly index, parse, and visualize threats. Raspberry Pis are single-board computers that can be purchased for around $40 [online](https://www.amazon.com/dp/B07BDR5PDW/ref=cm_sw_r_tw_dp_U_x_FOUuCb1KCGCQF). In this blog post, I will outline how to host a mini-SIEM on a Raspberry Pi 3.

I will specifically teach you how to install & configure an ELK (Elasticsearch, Logstash, and Kibana) stack to process alerts from SNORT, an Intrustion Detection System. Although, understand this software bundle is designed to run on enterprise-level equipment, not micro-controllers.

Therefore, this tutorial has three primary objectives: (1) get an ELK stack running on a Raspberry Pi, (2) develop universal plugins to support a broad range of security tools, and (3) design a dashboard for basic security analysis.

## Table of Contents
* Software Requirements
* Objective #1: Get an ELK Stack Running on a Raspberry Pi
  1. [How to Install and Configure Elasticsearch](#get-an-elk-stack-running-on-a-raspberry-pi)
  2. [How to Install and Configure Logstash](#how-to-install-and-configure-logstash)
  3. [How to Install and Configure Kibana](#how-to-install-and-configure-kibana)
* Objective #2: Develop Universal Plugins to Support a Broad Range of Security Tools
  1. [How to Configure Rsyslog](#how-to-configure-rsyslog)
    * [How to Install and Configure SNORT](#develop-universal-plugins-to-support-a-broad-range-of-security-tools)
  2. [How to Write Logstash Input Plugins](#how-to-write-logstash-input-plugins)
  3. [How to Write Logstash Filter Plugins](#how-to-write-logstash-filter-plugins)
    *  [Grok Patterns](#grok-patterns)
  4. [How to Write Logstash Output Plugins](#how-to-write-logstash-output-plugins)
  5. Supporting Other Security Tools
    * [Adding fail2ban](#supporting-other-security-tools)
    * [Adding ModSecurity](#adding-modsecurity)
* Objective #3: Design a Dashboard for Basic Security Analysis
  1. [How to Create an Index Pattern](#design-a-dashboard-for-basic-security-analysis)
  2. [How to Create a Visualization](#how-to-create-a-visualization)
  3. [How to Create a Dashboard](#how-to-create-a-dashboard)

## Software Requirements
I was previously successful in completing this project using Raspbian Stretch 9.8, Linux kernel 4.14.98-v7+, Open Java Development Kit (OpenJDK) version 1.8.0\_181, and Elastic Stack (a.k.a ELK stack) version 6.5.4. Please ensure you have at least these software versions prior to beginning. Commands for checking and installing the required versions are below.

**Raspbian Operating System**  
The command `lsb_release` displays information about the currently installed Linux Standard Base, otherwise known as “distribution” or operating system. Most folks just use “distro” as slang. When the command `lsb_release` is used with `-irc`, only the distro ID, distro release, and distro codename is shown.
```bash
lsb_release -irc
```
```bash
Distributor ID: Raspbian
Release:        9.8
Codename:       stretch
```

**Linux Kernel**  
The command `uname` is short for “Unix name” and displays information about the kernel when executed. When used with `-r`, only the kernel release or version is shown.
```bash
uname -r
```
```bash
4.14.98-v7+
```

**Java**  
To install the correct version of Java and two additional dependencies (required packages), use the following command.
```bash
sudo apt install openjdk-8-jdk libjffi-java libjffi-jni
```
```bash
java -version
```
```bash
openjdk version "1.8.0_181"
OpenJDK Runtime Environment (build 1.8.0_181-8u181-b13-2~deb9u1-b13)
OpenJDK Client VM (build 25.181-b13, mixed mode)
```

## Get an ELK Stack Running on a Raspberry Pi

## How to Install and Configure Elasticsearch
Elasticsearch is the ELK component responsible for indexing our security alerts and logs. To download it, use `wget`.
```bash
sudo wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.5.4.deb
```

Then, install it (`-i`) using the Debian package manager `dpkg`.
```bash
sudo dpkg -i elasticsearch-6.5.4.deb
```

Next, use your preferred text-editor to open the main Elasticsearch configuration file.
```bash
sudo vim /etc/elasticsearch/elasticsearch.yml
```

At the very least, specify the IP address you want Elasticsearch to communicate on. Below are the parameters I used.
```bash
cluster.name: siem.sky.net
node.name: node01.siem.sky.net
network.host: 192.168.1.44
discovery.type: single-node
xpack.ml.enabled: false
```

Since the Raspberry Pi 3 only has 1GB of RAM, we must be methodical about memory management while configuring our ELK stack. For example, use the command below to check-on your memory usage.
```bash
free total -th
```

To explain, we’re asking the `free` program to display the total (`-t`) Random-Access and Swap (disk-based) Memory usage in a human-readable (`h`) format. On my Pi, I got the output below. It indicates I have 751 Megabytes (MBs) available out of 1.0 Gigabytes (GBs) total.
```bash
              total        used        free      shared  buff/cache   available
Mem:           923M         87M         64M         20M        771M        751M
Swap:           99M          0B         99M
Total:         1.0G         87M        164M
```

With this in mind, configure Elasticsearch to use a max of 256MBs.
```bash
sudo vim /etc/elasticsearch/jvm.options
```
```bash
################################################################
## IMPORTANT: JVM heap size
################################################################
##
## You should always set the min and max JVM heap
## size to the same value. For example, to set
## the heap to 4 GB, set:
##
## -Xms4g
## -Xmx4g
##
## See https://www.elastic.co/guide/en/elasticsearch/reference/current/heap-size.html
## for more information
##
################################################################  

# Xms represents the initial size of total heap space
# Xmx represents the maximum size of total heap space  

-Xms256m
-Xmx256m
```

Systemctl is the program responsible for starting and stopping services on a Systemd-configured machine. Use it to start Elasticsearch.
```bash
sudo systemctl start elasticsearch
```

Previously, Linux machines primarily used initialization, or _init_, scripts designed according to the System V standard. Confusingly, some Systemd-configured Linux distros still support commands which are meant to support the System V ecosystem. For example, to verify Elasticsearch is running, you can use either one of the following two commands.
```bash
# sysV, the "old" way
sudo service elasticsearch status
```
```bash
# systemd, the "new" way
sudo systemctl status elasticsearch
```
```bash
* elasticsearch.service - Elasticsearch
  Loaded: loaded (/usr/lib/systemd/system/elasticsearch.service; enabled; vendor
  Active: active (running) since Sun 2019-01-20 21:42:46 UTC; 4s ago
    Docs: http://www.elastic.co
Main PID: 4865 (java)
  CGroup: /system.slice/elasticsearch.service
          +-4865 /usr/bin/java -Xms256m -Xmx256m -XX:+UseConcMarkSweepGC -XX:CMSI

Jan 20 21:42:46 pi02 systemd[1]: Started Elasticsearch.
```

Finally, put a tail on your Elasticsearch log to watch the daemon boot-up. This is a great place to check for any configuration or runtime errors. Make sure to specify the correct cluster name (if you did not specify a cluster name, your log file-path should be `/var/log/elasticsearch/elasticsearch.log`); you’re looking for a basic license validation.
```bash
sudo tail -f /var/log/elasticsearch/siem.sky.net.log
```

You can also verify Elasticsearch is running by using `nmap` (it is my preference to use over `netstat`).
```bash
sudo apt install nmap
```
```bash
sudo nmap 192.168.1.44 -p9200
```
```bash
Starting Nmap 7.40 ( https://nmap.org ) at 2019-02-23 13:51 GMT
Nmap scan report for 192.168.1.44
Host is up (0.00011s latency).
PORT     STATE  SERVICE
9200/tcp open  wap-wsp

Nmap done: 1 IP address (1 host up) scanned in 0.73 seconds
```

**NOTE:** Turn-off Elasticsearch before proceeding.
```bash
sudo systemctl stop elasticsearch
```

## How to Install and Configure Logstash
Logstash is the ELK component responsible for parsing our security alerts and logs. Use `wget` & `dpkg` like before to download and install the 6.5.4 version.
```bash
sudo wget https://artifacts.elastic.co/downloads/logstash/logstash-6.5.4.deb
```
```bash
sudo dpkg -i logstash-6.5.4.deb
```

Next, modify the configuration file below and restrict Logstash’s memory usage to 256MBs as well.
```bash
 sudo vim /etc/logstash/jvm.options
```
```bash
# JVM configuration

# Xms represents the initial size of total heap space
# Xmx represents the maximum size of total heap space

-Xms256m
-Xmx256m
```

Then, specify the following parameters in Logstash’s main configuration file (FYI, I’m using default ports for simplicity).
```bash
 sudo vim /etc/logstash/logstash.yml
```
```bash
 node.name: node01.siem.sky.net
 http.host: "192.168.1.44"
 xpack.monitoring.enabled: false
 xpack.management.enabled: false
```

Finally, start and monitor it for any errors.
```bash
sudo systemctl start logstash
```

It may take a few seconds to boot-up. Nonetheless, look for the message `Successfully started Logstash API endpoint`.
```bash
sudo tail -f /var/log/logstash/logstash-plain.log
```

**NOTE:** If you have no errors to resolve, turn-off Logstash before proceeding.
```bash
sudo systemctl stop logstash
```

## How to Install and Configure Kibana
Kibana is the ELK component responsible for visualizing our security alerts and notifications. Kibana version 6.5.4 depends on Node.js version 8.14. Use the steps below to download, extract, and copy this particular binary into the correct directory.
```bash
sudo wget https://nodejs.org/dist/v8.14.0/node-v8.14.0-linux-armv7l.tar.xz
```
```bash
sudo tar -xvf node-v8.14.0-linux-armv7l.tar.xz node-v8.14.0-linux-armv7l/bin/node --strip 2
```
```bash
sudo cp ./node /usr/local/bin/
```

With the correct Node.js version in-place, now install Kibana.
```bash
sudo wget https://artifacts.elastic.co/downloads/kibana/kibana-6.5.4-linux-x86_64.tar.gz
```
```bash
sudo mkdir /usr/share/kibana/
```
```bash
sudo tar -xvf kibana-6.5.4-linux-x86_64.tar.gz --strip 1 --directory /usr/share/kibana/
```

To explain, we just decompressed the contents of the Kibana `tar` file into the `/usr/share/kibana` directory while simultaneously removing the top-level wrapper (`--strip 1`) they were packaged in. Next, configure Kibana using similar parameters shown below.
```bash
sudo vim /usr/share/kibana/config/kibana.yml
```
```bash
server.port: 80
server.host: "192.168.1.44"
server.name: node01.siem.sky.net
elasticsearch.url: "http://192.168.1.44:9200"
logging.dest: /var/log/kibana.log
```

Finally, we need to (1) set-aside the Node.js binary Kibana came with, (2) force it to use the one we downloaded, and then, (3) write a Systemd service file to reference when starting and stopping Kibana.
```bash
sudo mv /usr/share/kibana/node/bin/node /usr/share/kibana/node/bin/node.bak
sudo ln -s /usr/local/bin/node /usr/share/kibana/node/bin/node
sudo vim /etc/systemd/system/kibana.service
```
```bash
[Unit]
Description=Kibana

[Service]
ExecStart=/usr/share/kibana/bin/kibana

[Install]
WantedBy=multi-user.target
```
**NOTE:** Stop Kibana before proceeding if you started it for any reason. Also, Kibana will run as **root**. Address this as you see fit.

## Develop Universal Plugins to Support a Broad Range of Security Tools
**NOTE:** I highly recommend running your security tools and sensors on a different node than the one you're using to run your mini-SIEM. For example, the following section about Rsyslog & SNORT are using a separate Raspberry Pi.

## How to Configure Rsyslog
With our first objective achieved, we can now work towards our next goal: developing universal Logstash plugins to support a broad range of security tools. For this demo, we’re going to focus on SNORT, an Intrusion Detection System. Using the next few steps as a template, you will also be able to set-up other tools just as easily. First, configure Rsyslog to ingest SNORT input (identified as Syslog facility, “local6”).
1.  Stop Rsyslog
```bash
sudo systemctl stop rsyslog
```
2.  Write a `.conf` file asking it to log SNORT input locally & remotely
```bash
sudo vim /etc/rsyslog.d/snort.conf
```
```bash
local6.*	/var/log/snort_alerts.log
local6.*	@192.168.1.44:5140
```
3.  Restart the daemon
```bash
sudo systemctl start rsyslog
```

## How to Install and Configure SNORT
Next, check-out a blog post I wrote called, [SNORT & Sniff: an IDS/IPS](https://www.yoursecurity.tech/snort-sniff-an-ids-ips.html) to get a basic installation and configuration of SNORT running. Then, update the main `snort.conf` file to include the parameter specified below.
```bash
sudo vim /etc/snort/snort.conf
```
```bash
output alert_syslog: LOG_LOCAL6 LOG_ALERT
```

## How to Verify SNORT Input is Getting Logged Locally & Remotely

1.  Start SNORT
```bash
sudo snort -i eth0 -c /etc/snort/snort.conf
```

2.  Test a SNORT rule (the tutorial I referenced creates a rule for ICMP traffic)
```bash
ping 192.168.1.44
```

3.  Check our local log (as specified in the first line of `/etc/rsyslog.d/snort.conf`)
```bash
sudo tail -f /var/log/snort_alerts.log
```

4.  Check outbound traffic destined for the Logstash-based Rsyslog server we’re about to setup
```bash
sudo tcpdump -n dst port 5140 -XX
```

## How to Write Logstash Input Plugins
Your collection of plugins is the catalyst for transforming data into information and eventually, decisions. The first one we need to write for our mini-SIEM is an Input plugin to recieve Rsyslog data.
```bash
 sudo vim /etc/logstash/conf.d/input-rsyslog-all.conf
```
```bash
 input {
   syslog {
     host => '192.168.1.44'
     port => 5140
     type => 'rsyslog'
   }
 }
```

## How to Write Logstash Filter Plugins
If you import data without parsing it correctly, you will eventually be left with a mountain of logs and no hope of understanding their context. Therefore, we need to also write a Filter plugin to specifically process Rsyslog messages (the “boats” our SNORT alerts will be shipped in).

## Grok Patterns
Before going further, you need to know _how to grok_. [By defintion](https://www.dictionary.com/browse/grok), to grok, means to understand something thoroughly and intuitively. Logstash defines their plugin of the same name as a means to, “\[parse\] unstructured event data into fields.” Let’s use the SNORT alerts below as a use-case.

**SNORT Alert Examples**
```bash
 Jan 24 05:25:05 pi01 snort: [1:3000001:0] ICMP traffic! {ICMP} 192.168.1.44 -> 192.168.1.38
 Jan 24 05:25:05 pi01 snort: [1:408:5] ICMP Echo Reply [Classification: Misc activity] [Priority: 3] {ICMP} 192.168.1.44 -> 192.168.1.38
```

**A Grok Pattern for Matching Rsyslog Messages Containing SNORT Alerts**  
The provided Rsyslog messages can be divided into two main sections: (A) the base of our Rsyslog message and (B) the actual message itself. Logstash supports a pattern called `%{SYSLOGBASE}` which will (contingent upon a match) parse a timestamp, Syslog facility, Syslog priority, logsource, and the name of the program that generated the Syslog message.

For a visual, (1) open [Grok Debugger](https://grokdebug.herokuapp.com), (2) copy & paste one of the provided SNORT alert examples into the box called _Input_, and then, (3) copy & paste `%{SYSLOGBASE}` into the box called _Pattern_.

You should get output similar to below. To explain, Grok variables like `%{SYSLOGBASE}` represent Regular Expressions, or Regex patterns that match an infinite number of values. For example, `%{SYSLOGBASE}` matches both SNORT alert examples provided as well as any other example consisting of a calendar month, calendar day, hour (double-digit format), etc.

Therefore, to completely parse our Rsyslog messages we must use Grok variables that can accurately summarize our expected values. At this point, you might ask, “how do I know what Grok patterns/variables exist?” Fortunately, the Grok Debugger website has a [page](https://grokdebug.herokuapp.com/patterns#) listing supported patterns and their associated Regex sentences. Try adding the Grok pattern `%{GREEDYDATA}` in the _Pattern_ box as another demonstration of how Grokking works.
```bash
# output goes here
```

To move on, here is a Grok pattern you can use to filter for Rsyslog messages containing SNORT alerts. I added `snort_rule`, `alert`, `protocol`, `src_ip`, and `dst_ip` to my Grok pattern as _field names_. If there’s a match, the corresponding values will be identified using the respective label.
```bash
 %{SYSLOGBASE} \[%{DATA:snort_rule}\] %{GREEDYDATA:alert} \{%{WORD:protocol}\} %{IPV4:src_ip} \-\> %{IPV4:dst_ip}
```

There’s one more thing. In your Logstash Filter plugin, you’re only going to focus on matching the contents of the Rsyslog message (part B, as described above). Below is an example designed to match both alerts provided earlier.
```bash
 sudo vim /etc/logstash/conf.d/filter-rsyslog-snort.conf
```
```bash
filter {
  grok {
    match => {"message" => "\[%{DATA:snort_rule}\] %{GREEDYDATA:alert} \{%{WORD:protocol}\} %{IPV4:src_ip} \-\> %{IPV4:dst_ip}"}    
  }

  grok {  
    match => {"message" => "\[%{DATA:snort_rule}\] %{GREEDYDATA:alert} \{%{WORD:protocol}\} %{IPV4:src_ip}\:%{INT:src_port} \-\> %{IPV4:dst_ip}\:%{INT:dst_port}"}    
  }  
}
```

## How to Write Logstash Output Plugins

Finally, we need to make sure our Logstash pipeline directs its output to our Elasticsearch daemon.
```bash
sudo vim /etc/logstash/conf.d/output-elasticsearch-all.conf
```
```bash
output {
  elasticsearch {
    hosts => ['http://192.168.1.44:9200']
  }
}
```

At this point, it is safe to restart Logstash.
```bash
sudo systemctl start logstash
```

Tail Logstash while it boots-up to catch any issues relating to the plugins you just wrote.
```bash
sudo tail -f /var/log/logstash/logstash-plain.log
```

If there’s no immediate errors, you should be able to see port `5140` open up. Again, this is our Logstash-based Rsyslog server we invoked with our Input plugin.
```bash
sudo nmap 192.168.1.44 -p5140
```

**NOTE:** If you have no errors to resolve, turn-off Logstash before proceeding.
```bash
sudo systemctl stop logstash
```

## Supporting Other Security Tools

To include other tools in our mini-SIEM, one has the option of using up a Rsyslog server like we did above or writing another Logstash Input plugin that can read a _living_ file. Below are two example Logtash Input plugins I wrote: one for `fail2ban` (automates blocking and preventing brute force attacks) and another for Apache’s ModSecurity (a web application firewall). I import the `fai2ban` log using `rsync` since I have it running on a different box.

**NOTE:** Writing Logstash plugins for fail2ban & ModSecurity is not required to get your mini-SIEM up and running. Feel free to skip these two sections.

## Adding fail2ban
```bash
sudo vim /etc/logstash/conf.d/input-file-fail2ban.conf
```
```bash
input {
  file {
    type => 'fail2ban'
    path => '/var/log/sensors/fail2ban.log'
    mode => 'tail'
  }
}
```

You may notice I also include another plugin Logstash provides called `geoip`. This feature allows me to geographically map IP addresses using the the “GeoLite2 City database” provided by Maxmind.
```bash
sudo vim /etc/logstash/conf.d/filter-file-fail2ban.conf
```
```bash
filter {
  grok {
    match => {"message" => "%{TIMESTAMP_ISO8601:timestamp} %{GREEDYDATA} \[%{WORD:service}\] Ban %{IPV4:src_ip}"}
    add_tag => 'banned'
  }

  geoip {
    source => 'src_ip'
    target => 'geo_ip'
  }
}
```

## Adding ModSecurity
Stop the daemons.
```bash
sudo service rsyslog stop
sudo service apache2 stop
```

Tell Rsyslog to locally & remotely log all messages originating from the “Local5” facility.
```bash
sudo vim /etc/rsyslog.d/apache2.conf
   local5.*	/var/log/apache2_alerts.log
   local5.*	@192.168.1.44:5140
```

Increase the Apache2 log level verbosity.
```bash
sudo vim /etc/apache2/apache2.conf
   LogLevel info
```

Tell Apache to log events (relating to the default website) under the “Local5” Rsyslog facility.
```bash
sudo vim /etc/apache2/sites-available/000-default.conf
   CustomLog "||/usr/bin/logger -t apache -i -p local5.notice" combined
```

Restart the daemons.
```bash
sudo service rsyslog start
sudo service apache2 start
```

Simulate a web request.
```bash
curl localhost
```

Verify Apache events are being logged locally.
```bash
sudo tail -f /var/log/apache2_alerts.log
```

Verify Apache events are being sent to a remote Rsyslog server listening on port 5140.
```bash
sudo tcpdump -n dst port 5140 -XX
```

In practice, I have the following plugins under my Logstash configuration directory. I personally like to follow the naming convention of `<plugin type>`\-`<source|destination>`\-`<tool>`.
```bash
sudo ls -al /etc/logstash/conf.d/
```
```bash
# input plugins
input-rsyslog-all.conf		# <-- reads rsyslog-based input
input-file-fail2ban.conf	# <-- reads fail2ban log replicated from elsewhere

# filter plugins
filter-rsyslog-snort.conf	# <-- parses SNORT input
filter-rsyslog-modsecurity.conf	# <-- parses ModSecurity (Apache) input
filter-file-fail2ban.conf	# <-- parses fail2ban input

# output plugin
output-elasticsearch-all.conf   # <-- sends everything to Elasticsearch
```

## Design a Dashboard for Basic Security Analysis
**NOTE:** The following steps were tested using Kibana 6.5.4.

With our second objective achieved, we can now safely turn-on our entire ELK stack and proceed to build a simple dashboard intended for security analysis. Make sure to give yourself about 10-20 minutes for your daemons to get situated (they’ll be fighting for memory). Alternatively, you can start one at a time, checking the logs and ports as before.
```bash
sudo systemctl start elasticsearch logstash kibana
```

Once you’re confident your mini-SIEM is fully operational (to include receiving messages from Rsyslog), open a web browser to the IP address you’ve been using. For example, my mini-SIEM can be accessed at `192.168.1.44`.

## How to Create an Index Pattern
You should not have to do this part often, but you must do it at least once.
1. Click-on **Management**
2. Click-on **Index Patterns**
3. Click-on **Create index pattern**
  * In the _Index Pattern_ text-box, type “logstash-\*”
  * Click-on **Next step**
4. Under the _Time Filter field name_ drop-box, select “_I don’t want to use the Time Filter_”
5. Click-on **Create index pattern**

## How to Create a Visualization
If it is not apparent, this portion is the “meat & potatoes” of your mini-SIEM. You’ll need to repeat the steps below for every widget you want on your dashboard. I initially ran into the problem of not knowing what I needed to know. To get you started, consider creating a _Visualization_ for each aspect of a SALUTE (Size, Activity, Location, Unit, Time, Equipment) report. In the US military, we use SALUTE reports to quickly summarize information and provide intelligence.
```bash
# table goes here
```

**How to Create a Visualization**
1. Click-on **Visualize**
2. Click-on **Create a visualization**
3. Click-on **Metric**
4. Click-on “_logstash-_ \*”
  * Click-on **Add a filter**
    * Specify the following:
      * _Fields_ = `program`
      * _Operators…_ = `is`
      * _Value…_ = `snort`
      * _Label_ = `Sensor`
    * Click-on **Save**
  * Click-on **Add a filter**
    * Specify the following:
      * _Fields_ = `alert`
      * _Operators…_ = `is`
      * _Value…_ = `ICMP traffic!`
      * _Label_ = `Alert`
    * Click-on **Save**
  * Under _Metrics_, click-on the arrow pointing down
    * In the _Custom label_ text-box, type “ICMP Alerts”
  * Click-on the arrow pointing sideways to apply
5. Click-on _Save_ (top-right)
  * Under the _Title_ text-box, type “SNORT: ICMP Alerts (Counter)”
  * Click-on **Confirm Save**

## How to Create a Dashboard
Follow these steps to add the widget you just created. As before, repeat/modify this process as necessary.
1. Click-on **Dashboard**
2. Click-on **Create new dashboard**
3. Click-on **Add** (top-right)
4. Select _SNORT: ICMP Alerts (Counter)_
5. Click-on **Save**
  * Under the _Title_ text-box, type “Security Posture”
  * Click-on **Confirm Save**

## Summary
In summary, we achieved our three objectives: (1) we got an ELK stack running on a Raspberry Pi, (2) we developed multiple, universal plugins to support a broad range of security tools, and (3) we designed a dashboard for basic security analysis. We were able to accomplish all of this by downloading and installing OpenJDK 1.8.0\_181, Node.js 8.14, and the 6.5.4 Debian & x86-based packages for an ELK stack. To support a greater number of security tools, we learned we must at least start with a Rsyslog-based or file-based Logstash Input plugin. Most importantly, we also realized a corresponding Grok pattern is key to correctly parsing any tool’s output.

## References
*   [The Security Stoic: Home Security Part III - ELK on a Raspberry Pi](https://thesecuritystoic.com/2017/08/home-security-iii-elk-on-a-raspberry-pi/)
*   [Raspberry Pi Documentation: Updating and Upgrading Raspbian](https://www.raspberrypi.org/documentation/raspbian/updating.md)
*   [Adafruit Learning System - Disk Space, Memory Use, and CPU Load: du, df, free, and w](https://learn.adafruit.com/an-illustrated-shell-command-primer/checking-file-space-usage-du-and-df)
*   [Elasticsearch Reference (6.5): Install Elasticsearch with Debian Package](https://www.elastic.co/guide/en/elasticsearch/reference/6.5/deb.html#deb-running-systemd)
*   [Elasticsearch Reference (2.4): Running as a Service on Linux](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/setup-service.html)
*   [opensource.com: Getting started with Logstash](https://opensource.com/article/17/10/logstash-fundamentals)
*   [Logstash Reference (6.5): Setting Up and Running Logstash - Logstash Directory Layout](https://www.elastic.co/guide/en/logstash/6.5/dir-layout.html)
*   [Logstash Reference (6.5): Input Plugins - Syslog input plugin](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-syslog.html#plugins-inputs-syslog-type)
*   [Logstash Reference (6.5): Filter Plugins - Grok filter plugin](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html)
*   [Logstash Reference (6.5): Output Plugins - Elasticsearch output plugin](https://www.elastic.co/guide/en/logstash/current/plugins-outputs-elasticsearch.html#plugins-outputs-elasticsearch-hosts)
*   [Logstash Reference (6.5): Field References Deep Dive - Reloading the Config File](https://www.elastic.co/guide/en/logstash/6.5/reloading-config.html)
*   [StackExchange - serverfault: How do I feed Elasticsearch my Snort alert log?](https://serverfault.com/questions/809156/how-do-i-feed-elasticsearch-my-snort-alert-log)
*   [Rapid7: How to Install Snort NIDS on Ubuntu Linux](https://blog.rapid7.com/2017/01/11/how-to-install-snort-nids-on-ubuntu-linux/)
*   [informit: Working with Snort Rules](http://www.informit.com/articles/article.aspx?p=101171&seqNum=6)
*   [How to Detect NMAP Scan Using Snort](https://www.hackingarticles.in/detect-nmap-scan-using-snort/)
*   [LinuxQuestions: Snort only captures traffic to localhost](https://www.linuxquestions.org/questions/linux-networking-3/snort-only-captures-traffic-to-localhost-4175433205/)
*   [US Army Research Lab: The Snort-Elasticsearch-Logstash-Kibana Stack](https://www.arl.army.mil/arlreports/2017/ARL-TR-8175.pdf)
*   [Grok Debugger](https://grokdebug.herokuapp.com)
*   [TecMint: How to Manage Systemd Services and Units Using Systemctl in Linux](https://www.tecmint.com/manage-services-using-systemd-and-systemctl-in-linux/)
*   [StackOverflow: Escaping double curly braces inside a markdown code block in Jekyll](https://stackoverflow.com/questions/24102498/escaping-double-curly-braces-inside-a-markdown-code-block-in-jekyll)
*   [Mark Sanborn: Extract without First Directory](https://www.marksanborn.net/linux/extract-without-first-directory/)
*   [SDL: Running the JVM in server mode](https://docs.sdl.com/LiveContent/content/en-US/SDL%20Tridion%20full%20documentation-v1/GUID-5B1E7AFA-9961-4AD7-B608-308279CA0F23)
*   [Raspberry Pi Forums: swap in Raspbian: More trouble than it is worth?](https://www.raspberrypi.org/forums/viewtopic.php?t=198177)
