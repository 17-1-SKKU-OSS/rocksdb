RocksDB 관련 자주 묻는 질문에 대한 답변입니다.

## RocksDB가 무엇인가요?

RocksDB는 빠른 저장을 위한 임베디드 영구 key-value 저장소입니다. RocksDB는 클라이언트-서버 데이터베이스의 기반으로 사용될 수도 있지만, 현재는 임베디드 작업환경에 초점을 두고 있습니다.

RocksDB는 [LevelDB](https://code.google.com/p/leveldb/)를 기반으로하여 많은 CPU 코어가있는 서버에서 실행되도록 확장 가능하며, 빠른 스토리지를 효율적으로 사용할 수 있고, IO 바인딩 지원, in-memory and write-once workloads를 지원하고 혁신을 가능하게하는 유연성을 제공합니다.

최신 정보를 보시려면, [Mark Callaghan’s and Igor Canadi’s talk at CMU on 10/2015](https://scs.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=f4e0eb37-ae18-468f-9248-cb73edad3e56)를 참고하세요. Data@Scale 2013 conference에서 진행된 [Dhruba Borthakur’s introductory talk](https://github.com/facebook/rocksdb/blob/gh-pages/intro.pdf?raw=true)에서는 RocksDB가 어떻게 진화했는지에 대해 확인할 수 있습니다.

## 성능비교는 어떤가요?

우리는 LevelDB의 성능 측정을 해보고 LevelDB가 우리의 서버 워크로드에는 적합하지 않다고 판단했습니다. LevelDB의 [benchmark results](http://leveldb.googlecode.com/svn/trunk/doc/benchmark.html)는 처음 보았을 때에는 멋지지만, 우리는 곧 그 결과가 테스트 머신의 RAM 크기보다 작은 사이즈의 데이터베이스에 대한 것이란 걸 깨달았습니다. – 전체 데이터베이스가 OS페이지 캐시에 모두 들어갈 수 있을 경우 말입니다. 메인 메모리보다 5배 이상 큰 데이터베이스에서 동일한 벤치 마크를 수행했을 때의 결과는 좋지 않았습니다.

이와 대조적으로, 우리는 Flash의 서버 워크로드에 대한 [RocksDB benchmark results](https://github.com/facebook/rocksdb/wiki/Performance-Benchmarks)를 게시했습니다. 우리는 LevelDB에도 이와 같은 서버 워크로드에 대해 벤치마크를 돌려 성능을 측정했고, RockDB는 이러한 IO 바인딩 워크로드에 대해 LevelDB보다 견고한 성능을 나타낸다는 것을 발견했습니다. 우리는 LevelDB의 단일 스레드 압축 프로세스가 서버 워크로드를 구동하기에 부족하다는 것을 알았습니다. LevelDB에서의 빈번한 write-stall는 P99 지연시간을 엄청나게 증가시킨 다는 것을 확인했습니다. 또한 OS 캐시에 파일을 mmaping하면 읽기에 대한 병목 현상이 발생한다는 것을 확인했습니다. 우리는 LevelDB가 기본 플래시 스토리지에서 제공하는 모든 IO를 낭비해버리도록 할 수 없습니다.


## RocksDB는 어디에 사용하는 것이 좋을까요?

RocksDB는 다음 예시와 같이 지연이 적은 데이터베이스 액세스가 필요한 응용프로그램에서 사용할 수 있습니다.:

* 웹사이트 사용자의 히스토리 및 상태를 저장하는 프로그램.
* 거대한 데이터 셋에 빠르게 액세스해야하는 스팸 탐지 프로그램.
* 실시간으로 데이터셋을 스캔해야하는 그래프 검색 쿼리.
* Hadoop의 캐시 데이터.이를 통해 응용프로그램은 실시간으로 Hadoop 데이터에 쿼리를 실행할 수 있습니다.
* 많은 수의 삽입/삭제를 지원하는 메시지 큐.

## RocksDB의 채택 규모는 어떤가요?

RocksDB는 페이스북의 여러 백엔드 시스템에서 사용되는 임베디드 스토리지 엔진입니다. 페이스북 뉴스피드의 백엔드에서는 RocksDB가 Centrifuge라는 다른 스토리지 엔진을 대체했으며, 페이스북 제품에서 사용하는 분산 키-값 저장소 서비스인 ZippyDB는 RocksDB에 의존성을 두고 있습니다. ZippyDB에 대한 더 자세한 정보는 [Muthu Annamalai’s talk at Data@Scale in Seattle](https://youtu.be/DfiN7pG0D0k)을 참고하세요. 소셜 그래프 인프라의 분산 그래프 쿼리 엔진인 Dragon은 RocksDB를 사용하여 데이터를 저장합니다. Parse는 2015년 초부터 RocksDB를 사용해 동작하고 있습니다-[MongoDB on RocksDB in production](http://blog.parse.com/announcements/mongodb-rocksdb-parse/).

RocksDB는 업계의 많은 그룹에 유용한 구성요소임이 입증되었습니다. 현재 RocksDB를 사용하는 프로젝트 목록을 보려면 github에있는 USERS.md 파일을 살펴보세요.

## 데이터베이스 스토리지 엔진으로써의 RockDB는 어떤가요?

페이스북 엔지니어링 팀은 RocksDB가 데이터베이스 스토리지 엔진으로써 큰 가능성을 가지고 있다고 확신합니다. 이것은 MongoDB와의 구현을 통해 입증되었습니다.: [MongoRocks](https://github.com/mongodb-partners/mongo-rocks)는 RocksDB기반의 MongoDB 스토리지 엔진입니다.

[MyRocks](https://code.facebook.com/posts/190251048047090/myrocks-a-space-and-write-optimized-mysql-database/)는 MySQL을 위한 RocksDB 기반의 스토리지 엔진입니다. RocksDB를 사용하면서 기존 MySQL 설치와 비교한 벤치마크에서 2배 향상된 압축과 10배 적은 쓰기 증폭을 달성할 수 있었습니다. 이 결과를 감안해, MyRocks를 웹스케일 MySQL 워크로드를 위한 솔루션으로 개발하는 작업이 진행중입니다. [GitHub](https://github.com/facebook/mysql-5.6)를 참고하세요!

## 오픈소스인 이유가 있나요?

우리는 RocksDB가 페이스북 이외에도 유용할 것이라고 판단해 [GitHub](http://github.com/facebook/rocksdb)에서 오픈소스 프로젝트로 공개했습니다. 우리는 소프트웨어 프로그래머와 데이터베이스 개발자가 사용해보고, 발전시키고, 그들의 작업에 맞춰 커스터마이징하기를 바랍니다. 또한 모던 데이터베이스 알고리즘의 효율성에 대해서 학계와 의견을 나누고자 했습니다. 
