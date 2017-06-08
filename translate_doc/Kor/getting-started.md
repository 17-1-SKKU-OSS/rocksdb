---
docid: getting-started
title: Getting started
layout: docs
permalink: /docs/getting-started.html
---

## Overview

RocksDB 라이브러리는 키-값 형식의 데이터 저장소를 제공합니다. 키와 값은 바이트 배열 형식이며, 키들은 사용자가 지정한 비교 함수에 따라 정렬되어 저장됩니다.

라이브러리는 Facebook Database Engineering Team에 의해 관리되며, [LevelDB](https://github.com/google/leveldb)(by Sanjay Ghemawat and Jeff Dean at Google.)에 기초를 두고있습니다.

이 문서는 RocksDB가 어떻게 사용되는지 몇가지 심플한 예제를 제공합니다. RocksDB가 만들어진 이유에 대해 보고싶다면, [Dhruba Borthakur’s introductory talk](https://github.com/facebook/rocksdb/blob/gh-pages/intro.pdf?raw=true) (from Data @ Scale 2013 conference.)를 참고하세요.

## Opening A Database

A rocksdb database has a name which corresponds to a file system directory. All of the contents of database are stored in this directory. The following example shows how to open a database, creating it if necessary:

```c++
#include <assert>
#include "rocksdb/db.h"

rocksdb::DB* db;
rocksdb::Options options;
options.create_if_missing = true;
rocksdb::Status status =
  rocksdb::DB::Open(options, "/tmp/testdb", &db);
assert(status.ok());
...
```

If you want to raise an error if the database already exists, add the following line before the rocksdb::DB::Open call:

```c++
options.error_if_exists = true;
```

## Status

You may have noticed the `rocksdb::Status` type above. Values of this type are returned by most functions in RocksDB that may encounter
an error. You can check if such a result is ok, and also print an associated error message:

```c++
rocksdb::Status s = ...;
if (!s.ok()) cerr << s.ToString() << endl;
```

## Closing A Database

When you are done with a database, just delete the database object. For example:

```c++
/* open the db as described above */
/* do something with db */
delete db;
```

## Reads And Writes

The database provides Put, Delete, and Get methods to modify/query the database. For example, the following code moves the value stored under `key1` to `key2`.

```c++
std::string value;
rocksdb::Status s = db->Get(rocksdb::ReadOptions(), key1, &value);
if (s.ok()) s = db->Put(rocksdb::WriteOptions(), key2, value);
if (s.ok()) s = db->Delete(rocksdb::WriteOptions(), key1);
```

## Further documentation

These are just simple examples of how RocksDB is used. The full documentation is currently on the [GitHub wiki](https://github.com/facebook/rocksdb/wiki).

Here are some specific details about the RocksDB implementation:

- [Architecture Guide](https://github.com/facebook/rocksdb/wiki/Rocksdb-Architecture-Guide)
- [Format of an immutable Table file](https://github.com/facebook/rocksdb/wiki/Rocksdb-Table-Format)
- [Format of a log file](https://github.com/facebook/rocksdb/wiki/Write-Ahead-Log-File-Format)
