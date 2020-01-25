---
title: Cisco Configuration Anonymizer
layout: post
categories: netdev
---
Netconan is an interesting [Github](https://github.com/Intentionet/netconan) project to remove sensitive information from Network configuration files. This is particularly useful in managed SP environment where we share configurations with the end-customer, usually in form of an LLD or SLD. 
Netconan can anonymize *many types of sensitive information*:

* Sensitive strings like passwords or SNMP community strings for many common network vendors.
* IPv4 and IPv6 addresses
* User-specified sensitive words
* User-specified AS numbers

[Netconan](https://github.com/Intentionet/netconan) is maintained by the same company as [Batfish](http://batfish.org), an interesting open source modeling tool. 