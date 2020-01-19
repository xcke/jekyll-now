---
title: Network Designers in the era of NetDevOps
layout: post
categories: netdev
---
It is not easy to define what DevOps and NetDevOps actually means for Network Engineering and design. Designers are developing solutions, and driving discussions with the customer. How customers are consuming the network services is changing rapidly, so the design practices. But, what is NetDevOps?  
> “[NetDevOps](https://blogs.cisco.com/developer/embrace-netdevops-part-1) brings the culture, technical methods, strategies, and best practices of DevOps to Networking.”

Personally for me NetDevOps means currently to try to use some form of automation and programmability in how we consume data, interact with systems and manage state in the network infrastructure. 

Core element of this is  a programming language that is easy enough to get started with, and has rich ecosystem in Network Engineering. This is currently Python, and I can tell you, I love to use Python. According to stack overflow [insights](https://insights.stackoverflow.com/survey/2019) Python is the fastest-growing major programming language today.
To understand Data better, we need to learn some of the modern data serialization formats, such as [JSON](https://tools.ietf.org/html/rfc7159), [YAML](https://yaml.org). An advantage of these is textual representation of machine readable data, but still in human readable format. These can easily mapped to Python data objects. 
Capturing automation efforts in a proper data model is key, and will drive how a project or solution will build the datastore or Single source of Truth (SSoT).Popular language to describe a data model is [YANG](https://tools.ietf.org/html/rfc7950), but any other way could work, for example a YAML schema file.

In order to prototype configurations, a template language is also required. [Jinja2](https://jinja.palletsprojects.com/en/2.10.x/) seems to be very popular, and easy to learn. Pushing complexity into the templates will provide more generic reusable code, and also our solution will require less template variations of design document or device configuration.  Network Engineers could develop and design Templates, while NDEs developing the code.

So textual representation of the data, data models, templates and code supports using a version control system in our project. This is part of Infrastructure-as-code (IaC) approach, that was defined in a [DEVNET](https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2019/pdf/DEVNET-1616.pdf) session like:

> Infrastructure as Code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files...” 

A declarative definition of the state of the network design and infrastructure can help us to create blueprints and re-use solutions. It also makes downstream automation more easy or possible using a Configuration Management Tool, such as Ansible. Using an ecosystem with the version control system (like as Gitlab) enables us the capability of building automated test and deployment solutions, pipelines, CI/CD pipelines. In Design, validation and testing might be more applicable, but if we are talking about general Network Infrastructure, our pipeline might actually deploy the configuration to production system automatically, if all tests were to be passed.

### The road from zero to hero
There are a number of books and other materials available to start with, just google for NetDevOps, or head to [DevNet](https://developer.cisco.com).  I believe it is best to _do it yourself_ and read, but should be more focused on the practical side. Try to identify little tasks in the day-to-day job and code automation around it. Some examples: 

- Manipulate IP prefixes using Python
- Write a configuration templating solution
- Generate design documents
- Build a self-service web portal using Flask

It is important to re-use from the community as much as you can and GitHub is a great place for this, but if you want to chat some of the people solving the same issue like this, head to the NetworkToCode slack channel, or write something in the comment:-)