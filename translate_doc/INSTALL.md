## Compilation

### 한국어 문서

**중요**: 실제 사용을 위해서 RocksDB를 구동한다면, 기본 명령어인
`make`나 `make all`을 사용해서 컴파일 하지 마세요. Debug 모드로 컴파일되기 때문에 속도가 떨어집니다.

RocksDB 라이브러리는 다른 라이브러리에 대한 의존성이 없기는 하지만,
설치가 권장되는 몇개의 압축 라이브러리가 있습니다 (아래 참조). 
C++11을 지원하는 최신 gcc/clang 환경에서 개발되고 있습니다. 

RocksDB를 컴파일 할 때 여러가지 옵션을 사용할 수 있습니다.:

* [권장] `make static_lib` release 모드에서 librocksdb.a, RocksDB static library를 컴파일합니다.

* `make shared_lib` release 모드에서 librocksdb.so, RocksDB shared library를 컴파일합니다.

* `make check` debug 모드에서 컴파일 한 뒤, 모든 유닛 테스트를 수행합니다.

* `make all` RocksDB static library와 모든 툴, 유닛 테스트들을 컴파일합니다.
툴들은 gflags에 의존성을 가지고 있으므로, gflags 설치 후 진행해야 합니다.
Debug 모드로 컴파일되기 때문에 `make all` 옵션은 테스트용으로만 사용하세요.

* 기본적으로 생성되는 바이너리는 컴파일하는 플랫폼에 최적화됩니다.
(`-march=native` 또는 이와 동등). 따라서 CPU에서 SSE4.2를 지원한다면, SSE4.2가 자동으로 활성화됩니다.
CPU가 SSE4.2를 지원하지 않을 때 경고문을 출력하고 싶다면, `USE_SSE=1 make static_lib` 옵션으로 빌드하거나,
CMake를 사용한다면 `cmake -DFORCE_SSE42=ON` 옵션으로 빌드하세요. 
이식 가능한 포터블 바이너리를 빌드하려면, make 커맨드 앞부분에 PORTABLE=1`을 추가하세요.
예:`PORTABLE=1 make static_lib`.

## Dependencies - 의존 라이브러리

* 다음의 압축 라이브러리들과 RocksDB를 링크시킬 수 있습니다:
  - [zlib](http://www.zlib.net/) - 데이터 압축 라이브러리.
  - [bzip2](http://www.bzip.org/) - 데이터 압축 라이브러리.
  - [snappy](http://google.github.io/snappy/) - 빠른 데이터 압축 라이브러리.
  - [zstandard](http://www.zstd.net) - 빠른 Real-time 데이터 압축 라이브러리.

* 툴들은 다음 라이브러리에 의존합니다.:
  - [gflags](https://gflags.github.io/gflags/) - command line flags processing 라이브러리. 
      gflags가 설치되지 않았더라도 RocksDB 라이브러리는 컴파일 가능합니다.

## 지원 플랫폼

* **Linux - Ubuntu**
    * gcc를 C++ 11이 지원되는 4.8 버전 이상으로 업데이트 합니다.
    * gflags를 설치합니다. 터미널에서 다음 커맨드를 입력합니다: `sudo apt-get install libgflags-dev`
      위의 명령이 동작하지 않는다면, 다음을 참조하세요:
      (http://askubuntu.com/questions/312173/installing-gflags-12-04)
    * snappy를 설치합니다. 터미널에서 다음 커맨드를 입력합니다:
      `sudo apt-get install libsnappy-dev`.
    * zlib 설치: `sudo apt-get install zlib1g-dev`.
    * bzip2 설치: `sudo apt-get install libbz2-dev`.
    * zstandard 설치: `sudo apt-get install libzstd-dev`.

* **Linux - CentOS / RHEL**
    * gcc를 C++ 11이 지원되는 4.8 버전 이상으로 업데이트 합니다:
      `yum install gcc48-c++`
    * gflags 설치:

              git clone https://github.com/gflags/gflags.git
              cd gflags
              git checkout v2.0
              ./configure && make && sudo make install

      **Notice**: 설치가 끝난 뒤, gflags의 include path를 `CPATH`로 환경변수에 추가해주세요. 
      lib path도 `LIBRARY_PATH`로 추가합니다. 기본설정으로 설치했다면, include path는 `/usr/local/include`,
      lib path는 `/usr/local/lib`입니다.

    * snappy 설치:

              sudo yum install snappy snappy-devel

    * zlib 설치:

              sudo yum install zlib zlib-devel

    * bzip2 설치:

              sudo yum install bzip2 bzip2-devel

    * ASAN 설치 (디버깅을 위해 선택적으로 설치):

              sudo yum install libasan

    * zstandard 설치:

             wget https://github.com/facebook/zstd/archive/v1.1.3.tar.gz
             mv v1.1.3.tar.gz zstd-1.1.3.tar.gz
             tar zxvf zstd-1.1.3.tar.gz
             cd zstd-1.1.3
             make && sudo make install

* **OS X**:
    * C++ 11이 지원되는 최신 C++ 컴파일러를 설치합니다:
        * XCode 업데이트: `xcode-select --install` (XCode App의 설정에서 설치해도 됩니다).
        * [homebrew](http://brew.sh/)를 통한 설치:
            * MacOS를 통한 개발이 처음이라면, 여전히 다음 명령을 실행해야 합니다: `xcode-select --install`
            * gcc 4.8 이상 버전을 설치하기 위해 `brew tap homebrew/versions; brew install gcc48 --use-llvm` 명령을 실행하세요.
    * `brew install rocksdb` 명령을 실행하세요.

* **iOS**:
  * 다음 명령 실행: `TARGET_OS=IOS make static_lib`. 
  rocksdb iOS library를 사용한 프로젝트를 빌드할 때에는, 두개의 중요한 사전 처리 매크로를 등록해야 합니다:
  `ROCKSDB_LITE`, `IOS_CROSS_COMPILE`

* **Windows**:
  * MS Visual Studio 13로 빌드하기 위해서는 Update 4가 설치되어야 합니다.
  * CMakeLists.txt를 읽고 진행하세요.

* **AIX 6.1**
    * AIX Toolbox rpms와 gcc 설치
    * 환경변수 등록:
  
             export PORTABLE=1
             export CC=gcc
             export AR="ar -X64"
             export EXTRA_ARFLAGS=-X64
             export EXTRA_CFLAGS=-maix64
             export EXTRA_CXXFLAGS=-maix64
             export PLATFORM_LDFLAGS="-static-libstdc++ -static-libgcc"
             export LIBPATH=/opt/freeware/lib
             export JAVA_HOME=/usr/java8_64
             export PATH=/opt/freeware/bin:$PATH
  
* **Solaris Sparc**
    * GCC 4.8이상 버전 설치
    * 환경변수 등록:

             export CC=gcc
             export EXTRA_CFLAGS=-m64
             export EXTRA_CXXFLAGS=-m64
             export EXTRA_LDFLAGS=-m64
             export PORTABLE=1
             export PLATFORM_LDFLAGS="-static-libstdc++ -static-libgcc"

