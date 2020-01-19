---
title: Network Designers in the era of NetDevOps
layout: post
categories: netdev
---
It is not easy to define what DevOps and NetDevOps means for network designers.
Designers developing solutions, and driving discussions with customers They not writing code normally, and not interacting with APIs. However network services will be more and more software driven, and this also changes how we design networks.
  
> “[NetDevOps](https://blogs.cisco.com/developer/embrace-netdevops-part-1) brings the culture, technical methods, strategies, and best practices of DevOps to Networking.”

At this point NetDevOps means for me to try to use some form of automation and programmability in how to consume data, interact with systems and manage state in the network infrastructure.

Core element of this is a programming language that is easy enough to get started with, and has rich ecosystem in Network Engineering. This is currently Python, and I love to use it. According to stack overflow [insights](https://insights.stackoverflow.com/survey/2019) Python is the fastest-growing major programming language today.

To understand and manage data better, we need to learn some of the modern data serialization formats, such as [JSON](https://tools.ietf.org/html/rfc7159), [YAML](https://yaml.org). An advantage of these languages is textual representation of machine readable data,but still in human readable format. These can easily mapped to Python data objects. Capturing automation efforts in a proper data model is key, and will drive how a project or solution will build the datastore or Single source of Truth (SSoT).Popular language to describe a data model is [YANG](https://tools.ietf.org/html/rfc7950), but any other way could work, for example a YAML schema file.

To prototype configurations, a template language is also required. [Jinja2](https://jinja.palletsprojects.com/en/2.10.x/) is very popular, and easy to learn. Pushing complexity into the templates will provide more generic reusable code, and the solution will require less template variations of design documents or device configurations.Network engineers could develop and design Templates, while network dev engineers developing the code.

Textual representation of data, data models, templates and code supports using a version control system in our project. This is part of an Infrastructure-as-code (IaC) approach, that was defined in a [DEVNET](https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2019/pdf/DEVNET-1616.pdf) session like:

> Infrastructure as Code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files...” 

A declarative definition of state of network design and infrastructure can help to create blueprints and re-use solutions. It also makes downstream automation more easy or possible using a Configuration Management Tool, such as Ansible. Using an ecosystem with the version control system (like Gitlab) enables the capability of building automated test and deployment solutions, CI/CD pipelines. In Design, validation and testing might be more applicable, but if we are talking about general Network Infrastructure Operation, our pipeline might actually deploy the configuration to production automatically, if all tests were to be passed.

### The road from zero to hero
There are a number of books and other materials available to start with, just google for NetDevOps, or head to [DevNet](https://developer.cisco.com).  I believe it is best to _do it yourself_ and read, but should be more focused on the practical side. Try to identify little tasks in the day-to-day job and code automation around it. Some examples: 

- Manipulate IP prefixes using Python
- Write a configuration templating solution
- Generate design documents
- Build a self-service web portal using Flask

It is important to re-use from the community as much as you can and GitHub is a great place for this, but if you want to chat some of the people solving the same issues like you, head to the NetworkToCode slack channel or write something in the comment:-)