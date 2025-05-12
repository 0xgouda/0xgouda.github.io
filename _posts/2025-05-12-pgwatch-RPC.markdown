---
layout: post
title:  "pgwatch RPC"
author: "Ahmed Gouda"
date:   2025-05-12
categories: postgresql gsoc 
---
## Acceptance

After a long journey of searching projects, interacting with the community and starring at the codebase late night trying to understand the workflow & resolve bugs i have been [accepted](https://summerofcode.withgoogle.com/programs/2025/projects/4Be9mT3p) at __PostgreSQL's GSoC'25 program__!!, And here i will be sketching the outline of my __Enhancements to pgwatch v3 rpc integration__ proposal deliverables. 

## pgwatch RPC integration

pgwatch is postgresql servers monitoring solution, it has an embedded grafana dashboard, built with scale in mind where a single instance can monitor thousands of servers and many other features.

our focus here is on the data exporting options, pgwatch has builtin support for exporting data to postgres (and its flavours), prometheus and json. for sure this won't be enough to all users, so they added an RPC client that can send the data to a server which is then responsible of routing the data to the user's desired data solution.

And an implementation of an RPC server that integrates with pgwatch has been developed [pgwatch3_rpc_server](https://github.com/destrex271/pgwatch3_rpc_server), it tries to provide support for the most common data solutions and formats, but it mainly serves as a template or a tutorial on how to integrate with pgwatch RPC so users can find it easy to develop their own custom RPC sinks.

And now some more enhancements are required to be added to this pgwatch3_rpc_server, which include:
1. Provide an authentication protocol for the current RPC implementation
2. Add support for TLS to protect auth keys and data sent
3. Additional sink implementations 
4. Improved developer experience for building custom sinks

### enhancements outline
- Authentication: Implement authentication middleware that uses API Keys to limit &
secure access to data sinks
  - (breaking change) the pgwatch rpc client will get updated to support api keys authentication

- TLS: Add self-signed certificates generation functionality and user their credentials to encrypt the connection
  - (breaking change) the pgwatch rpc client will get update to connect over TLS (probably skipping certificate validation)

- improved dev experience: create a data sink template that has detailed documentation demonstrating to users the interfaces they should implement
  - (this section may get changed as we are rethinking about the best way to enhance development experience but this is what it is up to for now)

- Addition Sinks:
  1. ​AWS Cloudwatch -> AWS’s metric analytics solution
  2. ElasticSearch -> Search and analytics engine
  3. Splunk -> Security and event-management solution
  4. MongoDB -> NoSql Flexible Schema logging
  5. Azure log analytics -> Azure’s log analytics solution
  6. Google cloud pub/sub -> Google’s messaging & event streaming solution

### Flow chart
![Flow Chart](/assets/images/Flowchart.jpg)