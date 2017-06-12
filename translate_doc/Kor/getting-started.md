## 개요

RocksDB 라이브러리는 키-값 형식의 데이터 저장소를 제공합니다. 키와 값은 바이트 배열 형식이며, 키들은 사용자가 지정한 비교 함수에 따라 정렬되어 저장됩니다.

라이브러리는 Facebook Database Engineering Team에 의해 관리되며, [LevelDB](https://github.com/google/leveldb)(by Sanjay Ghemawat and Jeff Dean at Google.)에 기초를 두고있습니다.

이 문서는 RocksDB가 어떻게 사용되는지 몇가지 심플한 예제를 제공합니다. RocksDB가 만들어진 이유에 대해 보고싶다면, [Dhruba Borthakur’s introductory talk](https://github.com/facebook/rocksdb/blob/gh-pages/intro.pdf?raw=true) (from Data @ Scale 2013 conference.)를 참고하세요.

## 데이터베이스 열기

rocksdb 데이터베이스에는 파일 시스템 디렉토리에 해당하는 이름이 있습니다. 데이터베이스의 모든 내용은이 디렉토리에 저장됩니다. 다음 예제에서는 데이터베이스를 열고, 필요한 경우 데이터베이스를 만드는 방법을 보여줍니다.:

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

만약 데이터베이스가 이미 존재할 때 에러를 출력하고 싶다면, 'rocksdb::DB::Open call' 전에 다음 코드를 추가하세요.:

```c++
options.error_if_exists = true;
```

## 상태

위의 코드에서 `rocksdb :: Status` 타입을 보셨을 것입니다. 이 타입의 값은 오류가 발생할 수있는 RocksDB 대부분의 함수에서 반환됩니다. 반환 값을 통해 해당 결과가 정상인지 확인하고 관련 오류 메시지를 출력 할 수 있습니다.:

```c++
rocksdb::Status s = ...;
if (!s.ok()) cerr << s.ToString() << endl;
```

## 데이터베이스 닫기

데이터베이스 작업이 끝났다면, 다음처럼 데이터베이스 객체를 지워주기만 하면 됩니다.:

```c++
/* open the db as described above */
/* do something with db */
delete db;
```

## 읽기&쓰기

수정/조회를 위해, Put, Delete, 그리고 Get 방식을 지원합니다. 다음 예제는 `key1`의 값을 `key2`로 옮깁니다.

```c++
std::string value;
rocksdb::Status s = db->Get(rocksdb::ReadOptions(), key1, &value);
if (s.ok()) s = db->Put(rocksdb::WriteOptions(), key2, value);
if (s.ok()) s = db->Delete(rocksdb::WriteOptions(), key1);
```

## 추가 정보

이 문서는 RocksDB사용을 위한 간단한 예제만을 포함하고 있습니다. 전체 문서는 [GitHub wiki](https://github.com/facebook/rocksdb/wiki)에서 읽어 볼 수 있습니다.

다음은 RocksDB 구현에 대한 몇 가지 구체적인 내용입니다.:

- [Architecture Guide](https://github.com/facebook/rocksdb/wiki/Rocksdb-Architecture-Guide)
- [Format of an immutable Table file](https://github.com/facebook/rocksdb/wiki/Rocksdb-Table-Format)
- [Format of a log file](https://github.com/facebook/rocksdb/wiki/Write-Ahead-Log-File-Format)

