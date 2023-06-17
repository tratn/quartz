---
title: Making sense of Shuffle hash join and Broadcast join in Spark
enableToc: false
---

## Shuffle hash join 

Shuffle hash join is a join strategy where repartitioning is based on join key. This is considered as a wide transformation since shuffling of data between partitions is required, hence the word "shuffle" in its name. 

Suppose we have partitions of dataset A and dataset B residing in executor nodes. Typically, there are 2 main steps involved in shuffle hash join.  

![Shuffle hash join](notes/images/spark-shufflehash-join.jpg)

First, based on the join key, Spark will shuffle data so that each partition contains records with the same key for both dataset A and dataset B. Once we have all the partitions together, Spark will take relation of smaller size (in this case, let's assume that it is dataset A) to create a hash table and perform hash join within the partition. 

## Broadcast join 

![Broadcast join](notes/images/spark-broadcast-join.jpg)

Broadcast join is another join strategy where a copy of the smaller dataset (such as A) will be broadcasted to all executors so that join operation can be done locally. This eliminates the cost incurred with shuffling data between partitions. 

Specifically, partitions of dataset A in executor nodes will be sent to the driver node. Dataset A is then broadcasted from driver to executor nodes. Now that each executor node has a copy of dataset A, join operation can be done locally within the node. 