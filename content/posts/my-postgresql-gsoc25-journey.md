+++
date = '2025-06-17T16:44:04+03:00'
draft = false
title = "My Postgresql GSoC'25 Journey"
tags = ["postgresql", "gsoc"]
+++

### Postgres community bonding

The first phase of GSoC is community bonding, where the mentee gets to know the community of his organization.

For Postgres, I started searching for the communication channels used by contributors, I found out about [Postgres mailing lists](https://www.postgresql.org/list/) which unfortuanetly i hadn't involved into it that much till now, but i also discovered the [PostgreSQL Hacking](https://discord.gg/GavkM38HNX) Discord channel and the Hacking Workshops they host every month to study and discuss PostgreSQL topics, which has been a great learning resource for me since then.

Also I started looking for PostgreSQL-related projects to contribute to and found out that there are not that many starter issues in the main PostgreSQL engine itself, but the good part is that there were a lot of other projects that integrate with Postgres and build on top of it, which had a good amount of marked starter issues to help aspiring contributors get started. One of them was [pg_duckdb](https://github.com/duckdb/pg_duckdb) the official PostgreSQL extension for DuckDB, where I opened a [PR adding SQLSmith CI tests](https://github.com/duckdb/pg_duckdb/pull/812).

### Coding Period

At the start of the coding period, I dived more into 
[pgwatch](https://github.com/cybertec-postgresql/pgwatch/graphs/contributors) 
and 
[pgwatch_rpc_server](https://github.com/destrex271/pgwatch3_rpc_server), 
reading as much from their code and testing them with various options 
and workloads to enhance my understanding of their workflow. 

That led me to discover some issues and suggest optimizations. 

#### Issues

- discovered a [segmentation violation issue](https://github.com/cybertec-postgresql/pgwatch/issues/766) in pgwatch that got immediately resolved and published under the [pgwatch v3.4.0 release](https://github.com/cybertec-postgresql/pgwatch/releases/tag/v3.4.0) 

#### Pull Requests

- [Change JSON sink to use one global encoder.](https://github.com/cybertec-postgresql/pgwatch/pull/772) 
- [Define sync metric operations as consts.](https://github.com/cybertec-postgresql/pgwatch/pull/781)
- [Move repeated RPC server registration logic to global listener.](https://github.com/destrex271/pgwatch3_rpc_server/pull/58)
- [Modify the handle sync metric function to listen repeatedly.](https://github.com/destrex271/pgwatch3_rpc_server/pull/56) 


Then I started working on my first milestone, adding TLS encryption support to pgwatch RPC sink. 

I spent most of the time searching (TLS, Golang TLS support, RPC & TLS integration, etc.), 
Also the mentor pointed out to me that they had worked on a similar idea in 
[another project](https://github.com/cybertec-postgresql/vip-manager/blob/master/checker/etcd_leader_checker.go), 
which helped me a lot while reading its code. 

#### Pull Requests

1. [Add TLS encryption to the Pgwatch RPC channel](https://github.com/cybertec-postgresql/pgwatch/pull/796)
2. [Update the Pgwatch RPC Server to use a TLS listener.](https://github.com/destrex271/pgwatch3_rpc_server/pull/64)

And voila, 
[pgwatch v3.6](https://github.com/cybertec-postgresql/pgwatch/releases/tag/v3.6.0) 
supports optional TLS encryption for data sent over the RPC channel

#### Example Usage

```bash
# pgwatch rpc server
export RPC_SERVER_CERT=server.crt
export RPC_SERVER_KEY=server.key
go run ./cmd/[receiver] [flags]

# pgwatch
go run ./cmd/pgwatch [flags] --sink=rpc://[host:ip]?sslrootca=ca.crt 
```

---

I continued reading pgwatch codebase, and I was able to dicover and resolve more issues.

#### Pull Requests

- [fix: `master only` sources shutdown](https://github.com/cybertec-postgresql/pgwatch/pull/813)
- [fix: `Reap()` Recovery state caching](https://github.com/cybertec-postgresql/pgwatch/pull/815)
- [fix: `--min-db-size-mb` not processing values < 8](https://github.com/cybertec-postgresql/pgwatch/pull/818)
- [fix: sources that don't use presets can't be updated](https://github.com/cybertec-postgresql/pgwatch/pull/831)
- [add measurements written log message to RPC sink](https://github.com/cybertec-postgresql/pgwatch/pull/829) 
- [fix `Source.Equal()` presets comparison](https://github.com/cybertec-postgresql/pgwatch/pull/831)

After that, I was supposed to start working on adding authentication support to the current RPC sink implementation, 
and that was when I discovered that the currently used `net/rpc` golang package is very basic and limited, 
although there were a couple of ways to implement our desired **Basic Auth** functionality with it, 
I saw that it will be better to migrate to a more robust RPC framework that is better maintained and has more functionalities.

[gRPC](https://grpc.io/), being the most famous RPC framework, was the first solution that came to my mind, 
so I started learning about it and thinking about its suitability for our use case, 
and its high performance, language-neutral nature, rich feature set, 
and large ecosystem made me decide to go ahead with it.

I started by presenting this idea to my mentors, pointing out its benefits and optimizations, 
and I was asked to implement a local draft PR 
so they can see it in action and estimate the amount of the change it would cause.

#### Pull Requests

- [gRPC PoC](https://github.com/0xgouda/pgwatch/pull/1)

They liked it, and the gRPC migration got approved, and our deliverables timeline got updated accordingly. 

Now I rewrote the RPC sink for pgwatch to be a gRPC client, and all sinks in pgwatch_rpc_server 
got migrated to gRPC, and finally, I re-added TLS support on top of gRPC and used interceptors to add Basic Auth support.

#### Pull Requests

- [migrate pgwatch rpc sink to grpc](https://github.com/cybertec-postgresql/pgwatch/pull/850)
- [add optional TLS support to grpc sink](https://github.com/cybertec-postgresql/pgwatch/pull/853)
- [add basic auth support to grpc sink](https://github.com/cybertec-postgresql/pgwatch/pull/862)
- [add docs for grpc sink](https://github.com/cybertec-postgresql/pgwatch/pull/867)
- [update `SyncMetricHandler` to use grpc](https://github.com/destrex271/pgwatch3_rpc_server/pull/80)
- [implement grpc listener](https://github.com/destrex271/pgwatch3_rpc_server/pull/81)
- [migrate file sinks to grpc](https://github.com/destrex271/pgwatch3_rpc_server/pull/82)
- [migrate db sinks to grpc](https://github.com/destrex271/pgwatch3_rpc_server/pull/83)
- [migrate kafka and llama sinks to grpc](https://github.com/destrex271/pgwatch3_rpc_server/pull/84)
- [Add server TLS support over gRPC](https://github.com/destrex271/pgwatch3_rpc_server/pull/88)
- [Add Auth interceptor](https://github.com/destrex271/pgwatch3_rpc_server/pull/87)
- [refactor sinks package](https://github.com/destrex271/pgwatch3_rpc_server/pull/86)
- [remove `*pb.go` files from git](https://github.com/destrex271/pgwatch3_rpc_server/pull/90)
- [mention optional TLS and auth envs in README](https://github.com/destrex271/pgwatch3_rpc_server/pull/95)

These features should be available soon under the pgwatch v4 release.

#### Example Usage
```bash
# pgwatch rpc server
export PGWATCH_RPC_SERVER_USERNAME="user"
export PGWATCH_RPC_SERVER_PASSWORD="pwd"

export PGWATCH_RPC_SERVER_CERT="/path/to/server.crt"
export PGWATCH_RPC_SERVER_KEY="/path/to/server.key"

go run ./cmd/[receiver] [flags]

# pgwatch v4
go run ./cmd/pgwatch [flags] --sink=grpc://user:pwd@[host:ip]?sslrootca=ca.crt
```

---

At the same time, new issues irrelevant to my pgwatch RPC work got opened, 
and my experience with reading the codebase and fixing issues in it 
made me able to reproduce and fix some of them :).

#### Pull Requests

- [allow metrics loading from folder](https://github.com/cybertec-postgresql/pgwatch/pull/889)
- [fix `updateSources()` query parameters number](https://github.com/cybertec-postgresql/pgwatch/pull/875)

Also, I started reading [pgwatch docs](https://pgwat.ch), 
and figured out some issues and submitted updated suggestions 
for them That got accepted and merged.

#### Pull Requests

- [remove hard-coded metric defs from docs](https://github.com/cybertec-postgresql/pgwatch/pull/870)
- [misc minor updates to docs](https://github.com/cybertec-postgresql/pgwatch/pull/871)
- [add note in sizing docs about metrics returning thousands of rows](https://github.com/cybertec-postgresql/pgwatch/pull/874)
- [update docs for metrics yaml folder configs](https://github.com/cybertec-postgresql/pgwatch/pull/892)

Then it was time to start working on improving developer experience for building custom sinks,
I researched and tested a lot of ideas for this, and finally found out that the simplest and most effective 
approach is to provide a mini **sinks development tutorial** that explains the different functions from the
pgwatch gRPC API, their parameters, return values, and how users can make use of the predefined server logic
to easily develop their own custom sinks.

#### Pull Requests

- [add sinks development tutorial](https://github.com/destrex271/pgwatch3_rpc_server/pull/92)

Now with pgwatch RPC client & servers optimized and migrated to gRPC, 
support for authentication and TLS encryption, 
and docs for developing custom RPC sinks were added; 
the remaining milestone was to provide additional sink implementations.

I discussed the various available options with my mentors, 
and they wanted new sinks for a Full-text search engine and Apache Iceberg.   

Also, I proposed adding a GCP pub/sub sink that publishes data to Google Cloud, 
then users can register subscribers (subs) to pull data from it, 
and hence be able to easily route the data to different data storage and analytics solutions, 
and its addition got approved.

#### Pull Requests

- [add ElasticSearch sink](https://github.com/destrex271/pgwatch3_rpc_server/pull/96)
- [add Google Cloud pub/sub sink](https://github.com/destrex271/pgwatch3_rpc_server/pull/99)

While working on the final apache iceberg sink, I faced a lot of difficulties due to the iceberg-go package
being not mature enough, with relatively poor documentation (at that time), I discussed this 
with the mentors, and we saw this a good opportunity to show the extensibility of gRPC
by developing the sink in another language that has a more powerful iceberg library, 
e.g., Python or Java.

#### Pull Requests

- [add python based apache iceberg sink](https://github.com/destrex271/pgwatch3_rpc_server/pull/101)


#### Usage Example
```bash
# ElasticSearch Sink
export ELASTIC_PASSWORD="es_password"
go run ./cmd/elasticsearch_receiver -port 1234 -addrs=<comma-sep-list-of-es-addrs> -user=<es-username> -ca-file=<es-ca-fle>

# Google Cloud pub/sub sink
go run ./cmd/gcp_pubsub_receiver --port 1235 --project-id <gcp-project-id>

# Apache Iceberg sink
export PYICEBERG_HOME="cmd/pyiceberg_receiver"
python3 cmd/pyiceberg_receiver -p 1236 -d <dir-path>

# pgwatch v4
go run ./cmd/pgwatch [FLAGS] --sink=grpc://localhost:1234 --sink=grpc://localhost:1235 --sink=grpc://localhost:1236 
```

---

### End of Journey

This wraps up my GSoC journey at PostgreSQL, contributing to the pgwatch project. 

It's been a wonderful experience that has hugely improved me both personally and technically, 
and filled me with love for the community and interest in contributing to PostgreSQL projects.

Also, huge shout-out to my very supportive, responsive, and highly talented mentors 
[Pavlo Golub](https://www.linkedin.com/in/pashagolub/)
and 
[Akshat Jaimini](https://www.linkedin.com/in/akshat-jaimini-05a610203)
for their help and guidance.