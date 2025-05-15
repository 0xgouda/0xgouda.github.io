---
layout: post
title:  "pgwatch RPC"
author: "Ahmed Gouda"
date:   2025-05-12
categories: postgresql gsoc 
---
## PostgreSQL GSoC Acceptance

After a long journey of searching projects, interacting with the community and starring at the codebase late night trying to understand the workflow & resolve bugs i have been [accepted](https://summerofcode.withgoogle.com/programs/2025/projects/4Be9mT3p) at __PostgreSQL's GSoC'25 program__!!, And here i will be sketching the outline of my __Enhancements to pgwatch v3 rpc integration__ proposal deliverables. 

## pgwatch RPC integration

[Pgwatch](https://github.com/cybertec-postgresql/pgwatch) is PostgreSQL servers monitoring solution, it has an embedded grafana dashboard, built with scale in mind where a single instance can monitor thousands of servers and many other features, [Pgwatch Docs](https://pgwat.ch). 

our focus here is on the data exporting options, pgwatch has builtin support for exporting data to postgres (and its flavours), prometheus and json. for sure this won't be enough to all users, so they added an RPC client that can send the data to a server which is then responsible of routing thayt data to the user's desired data solution.

And an implementation of an RPC server that integrates with pgwatch has been developed [pgwatch3_rpc_server](https://github.com/destrex271/pgwatch3_rpc_server), it tries to provide support for the most common data solutions and formats, but it mainly serves as a template or a tutorial on how to integrate with pgwatch RPC so users can find it easy to develop their own custom RPC sinks.

And now some more enhancements are required to be added to this pgwatch3_rpc_server, which include:
1. Provide an authentication protocol for the current RPC implementation
2. Add support for TLS to protect auth keys and data sent
3. Improve Architecture
4. Additional sink implementations 
5. Improved developer experience for building custom sinks

### Outline

1. Improved Architecture

    - All the codebase especially shared logic or repeated code will be defined in functions/interfaces to make the codebase more modular

2. ​Authentication

    - update pgwatch3 client has to send api keys with each RPC call
    - On receiver running it generates a robust api key and saves it to a file with strict permissions
    - The client copies the key and then Sends it with the parameters passed to the RPC function
    - The Server then uses a middleware function to Validate the api key parameter before processing the RPC call
    - On the receiver rerunning it overwrites the file invalidating the api key

3. TLS & HTTPs

    - update pgwatch3 client has to connect via TLS
    - On receiver running it runs a shell script that uses openssl to generate self-signed certificates and keys
    - Create a new tls listener and use the certificate/keys to encrypt the connection ​

4. ​Sink Template

    - Create a template receiver with only sink logic interfaces left to the user to implement with clear inline comments & README file to guide the developer how to implement those interfaces
    - Note: we are still rethinking other options to improve sinks development experience for users any suggestions are welcomed

5. Additional Sinks

    - implement more data sinks that both covers highly used solutions and improves the variety of the existing examples

    1. AWS Cloudwatch -> AWS’s metrics analytics solution
    2. ElasticSearch -> Search and analytics engine
    3. Splunk -> Security and event-management solution
    4. MongoDB -> NoSql Flexible Schema logging
    5. Azure log analytics -> Azure’s log analytics solution
    6. Google cloud pub/sub -> Google’s messaging & event streaming solution
    7. ...more

### Flow chart
![Flow Chart](/assets/images/Flowchart.jpg)