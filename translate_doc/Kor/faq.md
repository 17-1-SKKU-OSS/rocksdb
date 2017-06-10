---
docid: support-faq
title: FAQ
layout: docs
permalink: /docs/support/faq.html
---

RocksDB 관련 자주 묻는 질문에 대한 답변입니다.

## RocksDB가 무엇인가요?

RocksDB는 빠른 저장을 위한 임베디드 영구 key-value 저장소입니다. RocksDB는 클라이언트-서버 데이터베이스의 기반으로 사용될 수도 있지만, 현재는 임베디드 작업환경에 초점을 두고 있습니다.

RocksDB builds on [LevelDB](https://code.google.com/p/leveldb/) to be scalable to run on servers with many CPU cores, to efficiently use fast storage, to support IO-bound, in-memory and write-once workloads, and to be flexible to allow for innovation.


RocksDB는 [LevelDB](https://code.google.com/p/leveldb/)를 기반으로하여 많은 CPU 코어가있는 서버에서 실행되도록 확장 가능하며, 빠른 스토리지를 효율적으로 사용할 수 있고, IO 바인딩 지원, in-memory and write-once workloads를 지원하고 혁신을 가능하게하는 유연성을 제공합니다.

최신 정보를 보시려면, [Mark Callaghan’s and Igor Canadi’s talk at CMU on 10/2015](https://scs.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=f4e0eb37-ae18-468f-9248-cb73edad3e56)를 참고하세요. Data @ Scale 2013 conference에서 진행된 [Dhruba Borthakur’s introductory talk](https://github.com/facebook/rocksdb/blob/gh-pages/intro.pdf?raw=true)에서는 RocksDB가 어떻게 진화했는지에 대해 들을 수 있습니다.

## How does performance compare?

We benchmarked LevelDB and found that it was unsuitable for our server workloads. The [benchmark results](http://leveldb.googlecode.com/svn/trunk/doc/benchmark.html) look awesome at first sight, but we quickly realized that those results were for a database whose size was smaller than the size of RAM on the test machine – where the entire database could fit in the OS page cache. When we performed the same benchmarks on a database that was at least 5 times larger than main memory, the performance results were dismal.

By contrast, we’ve published the [RocksDB benchmark results](https://github.com/facebook/rocksdb/wiki/Performance-Benchmarks) for server side workloads on Flash. We also measured the performance of LevelDB on these server-workload benchmarks and found that RocksDB solidly outperforms LevelDB for these IO bound workloads. We found that LevelDB’s single-threaded compaction process was insufficient to drive server workloads. We saw frequent write-stalls with LevelDB that caused 99-percentile latency to be tremendously large. We found that mmap-ing a file into the OS cache introduced performance bottlenecks for reads. We could not make LevelDB consume all the IOs offered by the underlying Flash storage.

## What is RocksDB suitable for?

RocksDB can be used by applications that need low latency database accesses. Possibilities include:

* A user-facing application that stores the viewing history and state of users of a website.
* A spam detection application that needs fast access to big data sets.
* A graph-search query that needs to scan a data set in realtime.
* A cache data from Hadoop, thereby allowing applications to query Hadoop data in realtime.
* A message-queue that supports a high number of inserts and deletes.

## How big is RocksDB adoption?

RocksDB is an embedded storage engine that is used in a number of backend systems at Facebook. In the Facebook newsfeed’s backend, it replaced another internal storage engine called Centrifuge and is one of the many components used. ZippyDB, a distributed key value store service used by Facebook products relies RocksDB. Details on ZippyDB are in [Muthu Annamalai’s talk at Data@Scale in Seattle](https://youtu.be/DfiN7pG0D0k). Dragon, a distributed graph query engine part of the social graph infrastructure, is using RocksDB to store data. Parse has been running [MongoDB on RocksDB in production](http://blog.parse.com/announcements/mongodb-rocksdb-parse/) since early 2015.

RocksDB is proving to be a useful component for a lot of other groups in the industry. For a list of projects currently using RocksDB, take a look at our USERS.md list on github.

## How good is RocksDB as a database storage engine?

Our engineering team at Facebook firmly believes that RocksDB has great potential as storage engine for databases. It has been proven in production with MongoDB: [MongoRocks](https://github.com/mongodb-partners/mongo-rocks) is the RocksDB based storage engine for MongoDB.

[MyRocks](https://code.facebook.com/posts/190251048047090/myrocks-a-space-and-write-optimized-mysql-database/) is the RocksDB based storage engine for MySQL. Using RocksDB we have managed to achieve 2x better compression and 10x less write amplification for our benchmarks compared to our existing MySQL setup. Given our current results, work is currently underway to develop MyRocks into a production ready solution for web-scale MySQL workloads. Follow along on [GitHub](https://github.com/facebook/mysql-5.6)!

## Why is RocksDB open sourced?

We are open sourcing this project on [GitHub](http://github.com/facebook/rocksdb) because we think it will be useful beyond Facebook. We are hoping that software programmers and database developers will use, enhance, and customize RocksDB for their use-cases. We would also like to engage with the academic community on topics related to efficiency for modern database algorithms.
