---
title: From CCIE to Cloud Network Engineer
layout: post
date: 2019-03-21
categories: career
---
An interesting [article](https://cloud.google.com/blog/products/networking/from-ccie-to-google-cloud-network-engineer-four-things-to-think-about) about moving from enterprise networking space to cloud networking role from Tom Taggart. 

- In the cloud the focus is more on the Workflows and Endpoints, in contrast to network nodes and transit nature of the Traffic. 
- Building blocks are changing from traditional physical (or VM based) appliances and their requirements (e.g.: racks, cables, power outlets, etc.) to software based solutions. The promise here is that the added abstraction layer(s) will remove or hide many of the complex details associated with the old building blocks. 
- The network transport for the Cloud is mainly Internet based (for the Edge), however large players like Google, AWS, Azura are building out a global backbone that is parallel to the global internet backbone. This might provide advantages for traffic related to cloud services between geographic islands. (e.g.: VPC islands)
- Interaction with Infrastructure functions are possible using many of the client libraries, REST API or CLI-based SDK. Using a single Cloud provider provides uniform management-plane, and opens up the possibilities for an easy **Infrastructure-as-code** approach. 

I would not let go all the IETF RFC’s for now, but the transformation of how we interact with the Network Infrastructure is already changing in the direction what public cloud provides. 
