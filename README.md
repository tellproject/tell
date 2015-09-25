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
