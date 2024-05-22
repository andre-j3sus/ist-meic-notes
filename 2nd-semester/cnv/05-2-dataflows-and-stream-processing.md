# Dataflows and Stream Processing

* **✅ Advantages of MapReduce**:
  * Scalability;
  * Cheap commodity hardware;
  * **Procedural control**;
* **❌ Disadvantages**:
  * **Extremely rigid data flow** - M->R;
  * Common operations must be implemented from **scratch** - join, filter, sort, etc;
  * **Semantics hidden** inside map and reduce functions;
* Current programming models for clusters transform **data flowing** from stable storage to stable storage;
* Data flows are nice because the **runtime can decide where to run tasks, and can automatically recover from failures**;

## [Spark](https://spark.apache.org/)

* **Spark** makes working sets a first-class concept, to efficiently support two common types of applications that **repeatedly reuse a working set of data**:
  * **Iterative algorithms** - many machine learning algorithms are iterative;
  * **Interactive data mining** - users explore data through a series of queries;
* Provides a **distributed memory abstractions** for clusters to support apps with **working sets**;
* Data flow model with **RDDs (Resilient Distributed Datasets)**;
  * **Immutable** collections **partitioned** across the nodes of the cluster that can be **reused** across multiple parallel operations;
  * It is not needed to be materialized on disk;
  * Created by **RDD transformations** - map, filter, join, etc;
  * Can be **cached** in memory across operations;
  * **Parallel operations**: reduce, collect, count, etc;
  * Consistency is easy due to **immutability**.

### Spark Streaming

* **Spark Streaming** is an extension of the core Spark API that enables scalable, high-throughput, fault-tolerant stream processing of live data streams;
* Run a streaming computation as a **series of small batch computations** on small time intervals;

---

## [Flink](https://flink.apache.org/)

* Open-source **stream processing framework** with powerful stream and batch processing capabilities;
  * Low-latency;
  * High-throughput;
  * Stateful;
  * Distributed;
* Window types:
  * **Tumbling window** - non-overlapping windows;
  * **Sliding window** - overlapping windows;
  * **Count window** - fixed number of elements;
  * **Time-based window** - fixed time interval;
  * **Custom window**;