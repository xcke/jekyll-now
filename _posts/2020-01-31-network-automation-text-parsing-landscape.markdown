---
title: Network Automation Text Parsing Landscape
layout: post
categories: netdev
---
Command Line Interfaces (CLI) will be with us for a while now, as many network devices still not exposing configuration and operational data via APIs. Using CLI is still one of the most effective way of managing a single device from an engineers point of view, but it is not really good from application point of view.
Creating structured data is not easy,  we need to use screen scraping technics. Good news is we have many projects trying to solve this challenge: 

* [Template Text Parser](https://ttp.readthedocs.io/en/latest/Quick%20start.html): TTP is a Python module that allows fast performance parsing of semi-structured text data using Jinja2 like templates. TTP was developed to enable programmatic access to data produced by CLI of networking devices, but, it can be used to parse any semi-structured text that contains distinctive repetition patterns.

* [PyATS & Genie:](https://developer.cisco.com/docs/pyats/#!parsing-device-output)PyATS and Genie are Cisco libraries that have been developed by Cisco and released to the general public for a year or two now.

* [Parse Genie - Ansible Plugin:](https://github.com/clay584/parse_genie) The network genie filter takes unstructured network CLI command output from all Cisco network operating systems, and outputs structured data. While similar to other network CLI parsers already available (parse\_cli, parse\_cli\_textfsm), this parser is powered by a very mature and robust library written by Cisco called Genie (and underlying framework pyATS). 

* [NTC Templates](https://github.com/networktocode/ntc-templates): Repository of TextFSM Templates for Network Devices, and Python wrapper for TextFSM's CliTable. TextFSM is a project built by Google that takes CLI string output and passes each line through a series of regular expressions until it finds a match. The regular expressions use named capture groups to build a text table out of the significant text. The names of the capture groups are used as column headers, and the captured values are stored as rows in the table.

* [Yangify](https://yangify.readthedocs.io/en/latest/tutorials/intro.html): is a framework that allows you to easily write code that can map structured and non-structured data into data modelled using YANG models. Yangify can also do the opposite operation and convert data modelled with YANG models into other structured or non-structured data. This allows you to easily write code that can parse native output/data/configuration from network devices and map them into YANG models and vice-versa.

* [NAPALM](https://napalm.readthedocs.io/en/latest/): NAPALM is a library to provide a consistent, vendor-agnostic API to the network. It supports many different device types, and has a lot of functionality around interacting and managing a live network. 

This post was inspired by [https://operational.io/network-automation-text-parsing-landscape/](https://operational.io/network-automation-text-parsing-landscape/)

