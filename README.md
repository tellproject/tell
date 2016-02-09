# Tell
Tell is a distributed shared-data database developed at the [Systems Group](http://www.systems.ethz.ch) at [ETH Zurich](http://www.ethz.ch). It is named after the Swiss legend Wilhelm Tell. Tell aims to be a complete, transactional, distributed, in-memory data management system to run mixed workloads.

***DISCLAIMER***: Tell is currently pre-alpha and a research project. It is neither feature complete nor stable. You are free to use it but expect crashes, data-loss (durability is not implemented yet!!!), and other unwanted consequences!

This repository is a meta project and contains only links to repositories for it's components and a CMake file to build all components within one folder structure. Some components depend on others, others can be used in isolation. 

## Components

### TellStore
A simple data storage that stores relational data and does versioning (used for snapshot isolation support, but TellStore itself does not support transactions). It supports simple get and put request and additionally supports fast scans over tables. Furthermore it supports atomic operations on tuples.

### CommitManager
A service that decides on transaction ordering and gives snapshot descriptors to clients.

### Crossbow
A C++ library with shared functionality. This library contains general purpose functionality and could be interesting for other projects than tell. Amongst other things it contains an implementation of the epoch garbage collection strategy, a single-consumer-multiple-consumer lock-free queue, two different infiniband libraries (infinio is the one Tell uses), and a simple logging library.

### Bd-Tree
A latch-free B-Tree index that uses TellStore to store its nodes and do distributed indexing of data - this is used by Tell to implement indexes.

### TellDB
Currently just some benchmarks. But here should come the high level user interface for Tell. It will provide functionality to implement transactions in C++ and run them on TellStore.

### TellJava
A (currently unfinished) Java interface to TellStore, which gives read-only access to data stored in TellStore. This will be used to run Spark on top of TellStore.

## Building
The easiest way to build Tell is to checkout and build the whole project at once. To do so clone tell and execute the following commands:

```bash
git submodule update --init --recursive --remote
git submodule foreach --recursive git checkout master
```
This should clone all necessary code into the Tell project directory. Even tough all Tell modules are independent projects, they can be built at once. If you want TellJava to be built as well, make sure to set the JAVA_HOME environmnent variable to your JDK.

### Dependencies
Tell has the following dependencies, needed to be built:
- For Infiniband:
  - rdmacm
  - ibverbs
- boost
- Google Sparsehash
- Intel TBB
- JeMalloc
- LLVM
- Java 8 (if you want to build telljava)

Also please make sure to have a new enough (> 3.0) version of CMake installed on your system.

### How to build
Building, if all dependencies are installed, is easy. Just create a build directory and execute cmake followed by make:

```bash
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
make install
```

You don't have to install tell to use it (so you can skip the last command). As soon as everything is built, you should be able to start tellstore and a commit manager (you need both in order to be able to run transactions). Currently TellDB is unfinished (espiacially failure recovery is not implemented), but you can look at our simple benchmarks to see how we use it currently.

### Additional build options
If you have LLVM installed in a custom location, use the CMAKE_PREFIX_PATH flag for cmake which takes a semicolon-separated set of paths:

```bash
-DCMAKE_PREFIX_PATH="<path to LLVM>"
```

If you want to run Tell at its best performance, use link-time optimization and the proper comple flags, which means to add the following flags to cmake:

```bash
-DCMAKE_AR=/usr/bin/gcc-ar -DCMAKE_RANLIB=/usr/bin/gcc-ranlib -DCMAKE_CXX_FLAGS="-march=native -flto -fuse-linker-plugin"
```
