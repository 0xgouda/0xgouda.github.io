+++
date = '2025-06-17T16:44:04+03:00'
draft = false
title = "My Postgresql GSoC'25 Journey"
tags = ["postgresql", "gsoc"]
+++

- The post will be continuously updated. 

### Postgres community bonding

The first phase of GSoC is community bonding, where the mentee gets to know the community of his organization.

For Postgres, I started searching for the communication channels used by contributors, I found out about [Postgres mailing lists](https://www.postgresql.org/list/) which unfortuanetly i hadn't involved into it that much till now, but i also discovered the [PostgreSQL Hacking](https://discord.gg/GavkM38HNX) Discord channel and the Hacking Workshops they host every month to study and discuss PostgreSQL topics, which has been a great resource for me since then.

Then I started searching for currently ongoing PostgreSQL projects and unfortunately found out that there are not many low-hanging fruits in the main PostgreSQL engine itself, but the good part is that there were a lot of other projects that integrate with Postgres and build on top of it, which had a good amount of marked starter issues to help aspiring contributors get started. One of them was [pg_duckdb,](https://github.com/duckdb/pg_duckdb) the official PostgreSQL extension for DuckDB, where I opened a [PR adding SQLSmith CI tests](https://github.com/duckdb/pg_duckdb/pull/812)

### Coding Period

By the start of the coding period I started digging more into the pgwatch & pgwatch_rpc_server code and testing them with various workloads and options to enhance my understanding of their workflow, and good news! This gave back great results. 

1. I discovered a [segmentation violation issue](https://github.com/cybertec-postgresql/pgwatch/issues/766) that got immediately resolved and published under the [pgwatch v3.4.0 release](https://github.com/cybertec-postgresql/pgwatch/releases/tag/v3.4.0) 

- Also, reading the code gave me some enhancement ideas that I discussed with the maintainers, and most of them were approved. 
2. [Change JSON sink to use one global encoder.](https://github.com/cybertec-postgresql/pgwatch/pull/772) 
3. [Define sync metric operations as consts.](https://github.com/cybertec-postgresql/pgwatch/pull/781)
4. [Move repeated RPC server registration logic to global listener.](https://github.com/destrex271/pgwatch3_rpc_server/pull/58)
5. [Modify the handle sync metric function to listen repeatedly.](https://github.com/destrex271/pgwatch3_rpc_server/pull/56) 


Now I needed to start working on my first milestone which was adding TLS encryption support to the RPC channel. Similar to all the things I have worked on here, I spent most of the time searching (TLS, Golang TLS support, RPC & TLS integration, etc.). Also, the mentor pointed out to me that they had worked on a similar thing in [another project,](https://github.com/cybertec-postgresql/vip-manager/blob/master/checker/etcd_leader_checker.go) which helped me a lot reading its code, and yep, my first milestone got achieved!

1. [Add TLS encryption to the Pgwatch RPC channel](https://github.com/cybertec-postgresql/pgwatch/pull/796)
2. [Update the Pgwatch RPC Server to use a TLS listener.](https://github.com/destrex271/pgwatch3_rpc_server/pull/64)

- And voila, pgwatch now supports optional TLS encryption for data sent over the RPC channel

```bash
# having a PKI server.crt, server.key, ca.crt (the ca used to sign the first 2)

# pgwatch rpc server
export RPC_SERVER_CERT=server.crt
export RPC_SERVER_KEY=server.key
go run ./cmd/[receiver] [flags]

# pgwatch
go run ./cmd/pgwatch [flags] --sink=rpc://[host:ip]?sslrootca=ca.crt 
```

Also, as before I continued reading pgwatch code to understand the workflow and catch issues, and yep i caught some more.

1. [fix: `master only` sources shutdown](https://github.com/cybertec-postgresql/pgwatch/pull/813)
2. [fix: `Reap()` Recovery state caching](https://github.com/cybertec-postgresql/pgwatch/pull/815)
3. [fix: `--min-db-size-mb` not processing values < 8](https://github.com/cybertec-postgresql/pgwatch/pull/818)
4. [fix: sources that don't use presets can't be updated](https://github.com/cybertec-postgresql/pgwatch/pull/831)

After that i was supposed to start working on adding Auth Support to the current RPC implementation, 