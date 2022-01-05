---
layout: post
title: 'Windows Event Collection: Sysmon Subscription'
category: examples
permalink: 'examples/wec/sysmon-subscription'
---

```xml
<Subscription xmlns="http://schemas.microsoft.com/2006/03/windows/events/subscription">
	<SubscriptionId>Sysmon</SubscriptionId>
	<SubscriptionType>SourceInitiated</SubscriptionType>
	<Description></Description>
	<Enabled>true</Enabled>
	<Uri>http://schemas.microsoft.com/wbem/wsman/1/windows/EventLog</Uri>
	<ConfigurationMode>Normal</ConfigurationMode>
	<Delivery Mode="Push">
		<Batching>
			<MaxLatencyTime>900000</MaxLatencyTime>
		</Batching>
		<PushSettings>
			<Heartbeat Interval="900000"/>
		</PushSettings>
	</Delivery>
	<Query>
		<![CDATA[
<QueryList><Query Id="0"><Select Path="Microsoft-Windows-Sysmon/Operational">*[System[Provider[@Name='Microsoft-Windows-Sysmon'] and (Level=1  or Level=2 or Level=3 or Level=4 or Level=0 or Level=5)]]</Select></Query></QueryList>
		]]>
	</Query>
	<ReadExistingEvents>false</ReadExistingEvents>
	<TransportName>HTTP</TransportName>
	<ContentFormat>RenderedText</ContentFormat>
	<Locale Language="en-US"/>
	<LogFile>ForwardedEvents</LogFile>
	<PublisherName>Microsoft-Windows-EventCollector</PublisherName>
	<AllowedSourceNonDomainComputers>
		<AllowedIssuerCAList>
		</AllowedIssuerCAList>
	</AllowedSourceNonDomainComputers>
	<AllowedSourceDomainComputers>O:NSG:BAD:P(A;;GA;;;DC)S:</AllowedSourceDomainComputers>
</Subscription>
```
