---
title: Spark - an overview of architecture
enableToc: false
---

## Overview 

Spark is a popular open-source framework that provides several tools for analytics and processing of data in parallel fashion. While MapReduce-based systems often involve disk-expensive operations, computation in Spark avoids this by keeping jobs in memory. Let's examine the following components of Spark:
- Resilient Distributed Dataset
- Directed acyclic graph
- Resilient Distributed Dataset (RDD)

## Resilient Distributed Dataset

RDD is a Spark data structure representing a set of data that get partitioned and distributed among cluster nodes for parallel processing. There are 2 types of operations that can be performed on RDD: transformation and action

a. Transformation 

One of the characteristic of RDD is that it is immutable. If we would like to modify the data, we have to give Spark instructions on how to do it. These instructions are what called transformation. There are 2 types of transformation: narrow and wide. The former type doesn’t require data to be shuffled and reorganized between partitions while the latter does. Specifically, shuffling involves having input partitions contributing to other output partitions. Some of the basic RDD transformation operations that one would most likely to come across include map(), filter(),flatMap(), union(), etc. 

b. Action 

Actions are any functions that triggers the plan in transformation to be executed to return a result. To put this another way, when we apply transformation to RDD, those operations won’t get executed immediately but are postponed until an action is performed. This is because Spark uses lazy evaluation, a mechanism which allows Spark to optimize logical plan and convert it to a physical plan for execution. Examples of action operations include collect(), take(N), first() and count(). 

## Directed acyclic graph (DAG)

DAG can be think of as an object representing a logical flow of operations. It is used in the scheduling layer of Spark to convert logical execution plan to physical one. Recall that when we apply transformation onto RDD, they are lazily evaluated. This means that Spark doesn’t execute the operations right away but building up a logical plan of operations. In order to orchestrate such flow of operations, Spark uses DAG to define and schedule tasks.


## Bring it all together: How does a job get processed in Spark? 

In Spark, a job is a unit of parallel computation and is transformed into a DAG object to represent Spark’s execution plan. A job may consist of multiple stages, each of which is a node/vertex on the DAG. At the lowest abstraction level, a stage is further broken down into tasks, which are units of execution that get federated to executors. Each task maps to a single core and works on a single data partition.  Therefore, suppose we have an executor that has N cores and is capable of handling 1 or more task for each core, then we would have 1*N or more tasks working on N or more partitions in parallel. 