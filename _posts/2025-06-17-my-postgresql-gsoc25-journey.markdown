---
layout: post
title:  "My PostgreSQL GSoC'25 Journey"
author: "Ahmed Gouda"
date:   2025-06-17
categories: postgresql gsoc 
permalink: /:title/
---

- The Post will be Continuously Updated. 

### Postgres community bonding

The first phase of GSoC is Community Bonding where mentee gets to know the community of his Organization.

For Postgres i started searching for the communication channels used by contributors, i found out about [Postgres mailing lists](https://www.postgresql.org/list/) which unfortuanetly i hadn't involved into that much till now, but also i discovered the [PostgreSQL Hacking](https://discord.gg/GavkM38HNX) Discord channel and the Hacking Workshops they host every month to study and discuss PostgreSQL topics was a great thing for me.

Then i started searching about Currently on going PostgreSQL projects and unforuantely found out that there is not that much low hanging fruits in the main PostgreSQL engine itself but the Good part is that there was a lot of other Projects that integrate with Postgres and build on top of it which had a good amount of starter issues to help aspiring contributors get started one of them was [pg_duckdb](https://github.com/duckdb/pg_duckdb) The official PostgreSQL extension for DuckDB where i opened a [PR adding sqlsmith CI test](https://github.com/duckdb/pg_duckdb/pull/812)

### Coding Period

By The start of the coding period i started digging more into the pgwatch & pgwatch_rpc_server code and testing them with various workloads and options to enhance my understanding for their workflow and Good News! this give back Great results. 

1.  discovered a [segmentation violation issue](https://github.com/cybertec-postgresql/pgwatch/issues/766) that got immediately resolved and published under the [pgwatch v3.4.0 release](https://github.com/cybertec-postgresql/pgwatch/releases/tag/v3.4.0) 

- Also reading the code got me to have some enhancements ideas that i discussed with the maintainers and most of them were approved. 
2. [change Json sink to use one global encoder](https://github.com/cybertec-postgresql/pgwatch/pull/772) 
3. [define sync metric operations as consts](https://github.com/cybertec-postgresql/pgwatch/pull/781)
4. [move repeated rpc server registration logic to global listener](https://github.com/destrex271/pgwatch3_rpc_server/pull/58)
5. [modify the handle sync metric function to listen repeatedly](https://github.com/destrex271/pgwatch3_rpc_server/pull/56) 


Now i needed to start working on my first milestone which was adding TLS encryption support to RPC channel, similar to all the things i have worked on here i spent most of the time searching. (TLS, Golang TLS support, RPC & TLS integration, ...etc) also the mentor pointed to me that they have worked on similar thing in [another project](https://github.com/cybertec-postgresql/vip-manager/blob/master/checker/etcd_leader_checker.go) which helped me a lot reading its code, and Yep my first milestone got achieved!

1. [add TLS encryption to Pgwatch RPC channel](https://github.com/cybertec-postgresql/pgwatch/pull/796)
2. [update Pgwatch RPC Server to use TLS listener](https://github.com/destrex271/pgwatch3_rpc_server/pull/64)

- and voila pgwatch is now supporting optional TLS encryption for data sent over the RPC channel

```bash
# having a PKI server.crt, server.key, ca.crt (the ca used to sign the first 2)

# pgwatch rpc server
export RPC_SERVER_CERT=server.crt
export RPC_SERVER_KEY=server.key
go run ./cmd/[receiver] [flags]

# pgwatch
go run ./cmd/pgwatch [flags] --sink=rpc://[host:ip]?sslrootca=ca.crt 
```

Also as before reading old code was my way to understand the workflow and catch issues, and Yep i catched some more.

1. [fix `master only` sources shutdown](https://github.com/cybertec-postgresql/pgwatch/pull/813)
2. [fix `Reap()` Recovery state caching](https://github.com/cybertec-postgresql/pgwatch/pull/815)
3. [fix `--min-db-size-mb` not processing values < 8](https://github.com/cybertec-postgresql/pgwatch/pull/818)
4. [issue: sinks `BatchingDelay` is only applied for postgres sink](https://github.com/cybertec-postgresql/pgwatch/issues/821)
