# [Cloud Computing and Virtualization](https://fenix.tecnico.ulisboa.pt/disciplinas/AVExe23/2023-2024/2-semestre)

## Syllabus

1. Introduction;
   1. [Introduction to Cloud Computing and Virtualization](./01-1-introduction-to-cloud-computing-and-virtualization.md) - cloud computing, virtualization techniques and abstraction levels;
   2. [Core Emulation Techniques](./01-2-core-emulation-techniques.md) - interpretation and translation techniques;
   3. [System Virtualization](./01-3-system-virtualization.md) - ISA virtualizability, memory virtualization, paravirtualization and hardware support;
2. [Infrastructure-as-a-Service](./02-infrastructure-as-a-service.md) - overview of IaaS platforms, SLA, scaling architectures;
   1. [Amazon Web Services](./02-1-amazon-web-services.md) - EC2, CloudWatch, Auto Scaling, Elastic Load Balancing;
   2. [Microsoft Azure](./02-2-microsoft-azure.md) - Fabric Controller, Hyper-V;
   3. [OpenStack](./02-3-openstack.md) - Nova, Neutron, Swift;
3. Platform-as-a-Service;
   1. [High-Level Language VMs](./03-1-high-level-language-vms.md) - architecture overview, JVM, bytecode, security, garbage collection and JIT;
   2. [Cloud PaaS](./03-2-cloud-paas.md) - Google App Engine, Microsoft Azure, messaging services (Amazon SQS, Microsoft Azure Queues, Google Cloud Pub/Sub);
   3. [Function-as-a-Service](./03-3-function-as-a-service.md) - overview, platforms (AWS Lambda, Google Cloud Functions, Azure Functions, IBM OpenWhisk), design and implementation challenges, advanced FaaS apps (ExCamera and Lambada) and FaaS optimizations;
4. [Cloud Storage](./04-cloud-storage.md) - Object Storage (Amazon S3, Azure Blob Storage), File Storage (Google File System, Hadoop Distributed File System), Table Storage (Amazon DynamoDB, Google BigTable), NoSQL (Google Datastore);
5. Big-Data Processing
   1. [MapReduce](./05-big-data-processing-mapreduce.md) - MapReduce programming model, examples (Word Count, Page Rank), MapReduce in Hadoop architecture (master, worker nodes, splits, tasktracker and jobtracker);
   2. [Dataflows and Stream Processing](./05-2-dataflows-and-stream-processing.md) - Spark (RDDs, Spark Streaming), Flink;
6. Cross-cutting Issues

## Recap

Here are some notes from two courses I took in my bachelor's degree that are related to this course:

* [Systems Virtualization Techniques](https://github.com/andre-j3sus/isel-leic-notes/tree/main/5th-semester/tvs);
* [Cloud Computing](https://github.com/andre-j3sus/isel-leic-notes/tree/main/6th-semester/cn);

## Labs/Tools

1. [Pin](./labs/01-pin.md) - dynamic binary instrumentation tool;
2. [AWS](./labs/02-aws.md) - Amazon Web Services;