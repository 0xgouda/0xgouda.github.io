+++
date = "2025-05-12T16:44:04+03:00"
draft = false
title = "GSoC'25 at PostgreSQL: Acceptance"
tags = ["postgresql", "gsoc"]
aliases = ["/postgresql/gsoc/2025/05/12/pgwatch-RPC.html"]
weight = 1
+++

### What is GSoC?

[Google Summer of Code](https://summerofcode.withgoogle.com/) or GSoC is a global, 
online mentoring program focused on bringing new contributors into the open-source software world.

Every year, the GSoC [timeline](https://developers.google.com/open-source/gsoc/timeline) is announced, with the following steps:

1. Organizations application period  
    - Orgs willing to provide mentorship for new contributors start submitting their applications to the GSoC team. 

2. Project ideas discussion  
    - Potential contributors start discussing project ideas they can work on with the accepted orgs.  
    - In practice, most orgs already have a GSoC project ideas list published for that year, e.g., [Postgres GSoC'25 Ideas List](https://wiki.postgresql.org/wiki/GSoC_2025).

3. Contributor application period  
    - Contributors start submitting their project proposals.  

4. Accepted contributors announced

### My Application Process

At the beginning of February, I started looking at orgs that participated in GSoC'24 to see if they had published their GSoC'25 ideas list. 

I targeted organizations that weren't very competitive and have project ideas that match my skill set. 

After some searching, I found a project I thought I could contribute to. 

I started building it, reading the documentation, and reaching out to its mentors, 
but unfortunately, their responses were quite slow, 
which made me feel that my chances of making real progress with this project were pretty low.

That was frustrating, and I had to start searching for a new project in a different org.  

During that search, I discovered that the [Postgres GSoC'25 Ideas List](https://wiki.postgresql.org/wiki/GSoC_2025) 
contained a project very similar to the one I had previously worked on. 

Although Postgres was a very competitive org, I decided to give it a try anyway, 
and started exploring the project as before -- building it, reading its code, etc. 

And during that I discovered a small typo in a CLI flag description and submitted a PR to fix it that got merged!

#### Pull Requests

- [Fix typo in flag description](https://github.com/destrex271/pgwatch3_rpc_server/pull/40)

That was my first-ever open-source contribution, and my adrenaline levels got very high, 
thinking this is a very big thing to put in the project proposal :).  

Even though there were other contributors targeting GSoC who made much better contributions, 
I still felt this project was my best opportunity.  

From then until the application deadline, I actively asked the mentors questions on slack, requested feedback on my proposal drafts, studied the project in more depth, and made more contributions. 

#### Pull Requests

- [fix: prevent SQL injection in table creation by sanitizing table name](https://github.com/destrex271/pgwatch3_rpc_server/pull/42)
- [Fix: Validate and parse port number correctly](https://github.com/destrex271/pgwatch3_rpc_server/pull/44)
- [Implement main.go file in the parquet receiver](https://github.com/destrex271/pgwatch3_rpc_server/pull/49)
- [Modify the HandleSyncMetric() function in all sinks to listen repeatedly](https://github.com/destrex271/pgwatch3_rpc_server/pull/56)

### Acceptance

After this journey of searching projects, interacting with the community, 
and staring at the codebase late-night trying to understand the workflow and resolve bugs, 
I got [accepted](https://summerofcode.withgoogle.com/programs/2025/projects/4Be9mT3p) 
at __PostgreSQL's GSoC'25 program__!!.  

### Project

I got accepted for the
[Enhancements to pgwatch v3 RPC integration](https://wiki.postgresql.org/wiki/GSoC_2025#Enhancements_to_pgwatch_v3_RPC_integration) 
project, which targets improvements to pgwatch RPC client and pgwatch3_rpc_server, including:

- Providing an authentication protocol for the current RPC implementation
- Supporting TLS encryption over the RPC channel
- Improving Architecture
- Adding more sink implementations 
- Improving developer experience for building custom sinks

### Project Context

[Pgwatch](https://github.com/cybertec-postgresql/pgwatch) is a flexible PostgreSQL monitoring solution that offers a comprehensive view of database performance and health. 

It provides a user-friendly interface through Grafana dashboards and is built to scale, allowing a single instance to monitor thousands of servers. See [pgwatch Docs](https://pgwat.ch).

Supports writing gathered metrics to a Postgres database and its variants, Prometheus scrapper, JSON file, and an RPC client sink.

[pgwatch3_rpc_server](https://github.com/destrex271/pgwatch3_rpc_server) 
was developed as a collection of RPC servers that integrate with pgwatch.

It implements common data solutions but mainly aims to provide the building blocks that users can use to easily integrate with pgwatch and build their own production-ready RPC servers.

### Flow chart
![Flow Chart](/Flowchart.jpg)