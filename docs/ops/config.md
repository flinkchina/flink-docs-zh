---
title: "配置"
nav-id: "config"
nav-parent_id: ops
nav-pos: 4
---
<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

**对于单节点设置，Flink已准备好，即开箱即用，您无需更改默认配置即可开始使用.**

开箱即用的配置将使用您的默认Java安装。 如果要手动覆盖要使用的Java运行时，可以在`conf/flink-conf.yaml`中手动设置环境变量`JAVA_HOME`或配置键`env.java.home`。

此页面列出了设置性能良好(分布式)安装通常需要的最常见选项。此外，这里列出了所有可用配置参数的完整列表。

所有配置都在`conf/flink-conf.yaml`中完成，预计这将是[YAML键值对][YAML key value pairs](http://www.yaml.org/spec/1.2/spec.html)的平面集合，格式为`key：value`。

系统和运行脚本在启动时解析配置。 对配置文件的更改需要重新启动Flink JobManager和TaskManagers。

TaskManagers的配置文件可能不同，Flink不假设集群中有统一的机器。

* This will be replaced by the TOC
{:toc}

## 通用选项

{% include generated/common_section.html %}

## 全部参考

### HDFS

<div class="alert alert-warning">
  <strong>注意:</strong> 这些键已弃用，建议使用环境变量配置Hadoop路径 <code>HADOOP_CONF_DIR</code> 替代.
</div>

These parameters configure the default HDFS used by Flink. Setups that do not specify a HDFS configuration have to specify the full path to HDFS files (`hdfs://address:port/path/to/files`) Files will also be written with default HDFS parameters (block size, replication factor).

这些参数配置Flink使用的默认HDFS。没有指定HDFS配置的设置必须指定到HDFS文件的完整路径(`hdfs://address:port/path/to/files`)文件也将使用默认的HDFS参数(块大小、复制因子)来编写。

- `fs.hdfs.hadoopconf`: The absolute path to the Hadoop File System's (HDFS) configuration **directory** (OPTIONAL VALUE). Specifying this value allows programs to reference HDFS files using short URIs (`hdfs:///path/to/files`, without including the address and port of the NameNode in the file URI). Without this option, HDFS files can be accessed, but require fully qualified URIs like `hdfs://address:port/path/to/files`. This option also causes file writers to pick up the HDFS's default values for block sizes and replication factors. Flink will look for the "core-site.xml" and "hdfs-site.xml" files in the specified directory.

- `fs.hdfs.hdfsdefault`: The absolute path of Hadoop's own configuration file "hdfs-default.xml" (DEFAULT: null).

- `fs.hdfs.hdfssite`: The absolute path of Hadoop's own configuration file "hdfs-site.xml" (DEFAULT: null).

### Core

{% include generated/core_configuration.html %}

### JobManager

{% include generated/job_manager_configuration.html %}

### TaskManager

{% include generated/task_manager_configuration.html %}

### Distributed Coordination (via Akka)

{% include generated/akka_configuration.html %}

### REST

{% include generated/rest_configuration.html %}

### Blob Server

{% include generated/blob_server_configuration.html %}

### Heartbeat Manager

{% include generated/heartbeat_manager_configuration.html %}

### SSL Settings

{% include generated/security_configuration.html %}

### Network communication (via Netty)

These parameters allow for advanced tuning. The default values are sufficient when running concurrent high-throughput jobs on a large cluster.

{% include generated/netty_configuration.html %}

### Web Frontend

{% include generated/web_configuration.html %}

### File Systems

{% include generated/file_system_configuration.html %}

### Compiler/Optimizer

{% include generated/optimizer_configuration.html %}

### Runtime Algorithms

{% include generated/algorithm_configuration.html %}

### Resource Manager

The configuration keys in this section are independent of the used resource management framework (YARN, Mesos, Standalone, ...)

{% include generated/resource_manager_configuration.html %}

### YARN

{% include generated/yarn_config_configuration.html %}

### Mesos

{% include generated/mesos_configuration.html %}

#### Mesos TaskManager

{% include generated/mesos_task_manager_configuration.html %}

### High Availability (HA)

{% include generated/high_availability_configuration.html %}

#### ZooKeeper-based HA Mode

{% include generated/high_availability_zookeeper_configuration.html %}

### ZooKeeper Security

{% include generated/zoo_keeper_configuration.html %}

### Kerberos-based Security

{% include generated/kerberos_configuration.html %}

### Environment

{% include generated/environment_configuration.html %}

### Checkpointing

{% include generated/checkpointing_configuration.html %}

### RocksDB State Backend

{% include generated/rocks_db_configuration.html %}

### Queryable State

{% include generated/queryable_state_configuration.html %}

### Metrics

{% include generated/metric_configuration.html %}

### RocksDB Native Metrics
Certain RocksDB native metrics may be forwarded to Flink's metrics reporter.
All native metrics are scoped to operators and then further broken down by column family; values are reported as unsigned longs. 

<div class="alert alert-warning">
  <strong>Note:</strong> Enabling native metrics may cause degraded performance and should be set carefully. 
</div>

{% include generated/rocks_db_native_metric_configuration.html %}

### History Server

You have to configure `jobmanager.archive.fs.dir` in order to archive terminated jobs and add it to the list of monitored directories via `historyserver.archive.fs.dir` if you want to display them via the HistoryServer's web frontend.

- `jobmanager.archive.fs.dir`: Directory to upload information about terminated jobs to. You have to add this directory to the list of monitored directories of the history server via `historyserver.archive.fs.dir`.

{% include generated/history_server_configuration.html %}

## Legacy

- `mode`: Execution mode of Flink. Possible values are `legacy` and `new`. In order to start the legacy components, you have to specify `legacy` (DEFAULT: `new`).

## Background


### Configuring the Network Buffers

If you ever see the Exception `java.io.IOException: Insufficient number of network buffers`, you
need to adapt the amount of memory used for network buffers in order for your program to run on your
task managers.

Network buffers are a critical resource for the communication layers. They are used to buffer
records before transmission over a network, and to buffer incoming data before dissecting it into
records and handing them to the application. A sufficient number of network buffers is critical to
achieve a good throughput.

<div class="alert alert-info">
Since Flink 1.3, you may follow the idiom "more is better" without any penalty on the latency (we
prevent excessive buffering in each outgoing and incoming channel, i.e. *buffer bloat*, by limiting
the actual number of buffers used by each channel).
</div>

In general, configure the task manager to have enough buffers that each logical network connection
you expect to be open at the same time has a dedicated buffer. A logical network connection exists
for each point-to-point exchange of data over the network, which typically happens at
repartitioning or broadcasting steps (shuffle phase). In those, each parallel task inside the
TaskManager has to be able to talk to all other parallel tasks.

<div class="alert alert-warning">
  <strong>Note:</strong> Since Flink 1.5, network buffers will always be allocated off-heap, i.e. outside of the JVM heap, irrespective of the value of <code>taskmanager.memory.off-heap</code>. This way, we can pass these buffers directly to the underlying network stack layers.
</div>

#### Setting Memory Fractions

Previously, the number of network buffers was set manually which became a quite error-prone task
(see below). Since Flink 1.3, it is possible to define a fraction of memory that is being used for
network buffers with the following configuration parameters:

- `taskmanager.network.memory.fraction`: Fraction of JVM memory to use for network buffers (DEFAULT: 0.1),
- `taskmanager.network.memory.min`: Minimum memory size for network buffers (DEFAULT: 64MB),
- `taskmanager.network.memory.max`: Maximum memory size for network buffers (DEFAULT: 1GB), and
- `taskmanager.memory.segment-size`: Size of memory buffers used by the memory manager and the
network stack in bytes (DEFAULT: 32KB).

#### Setting the Number of Network Buffers directly

<div class="alert alert-warning">
  <strong>Note:</strong> This way of configuring the amount of memory used for network buffers is deprecated. Please consider using the method above by defining a fraction of memory to use.
</div>

The required number of buffers on a task manager is
*total-degree-of-parallelism* (number of targets) \* *intra-node-parallelism* (number of sources in one task manager) \* *n*
with *n* being a constant that defines how many repartitioning-/broadcasting steps you expect to be
active at the same time. Since the *intra-node-parallelism* is typically the number of cores, and
more than 4 repartitioning or broadcasting channels are rarely active in parallel, it frequently
boils down to

{% highlight plain %}
#slots-per-TM^2 * #TMs * 4
{% endhighlight %}

Where `#slots per TM` are the [number of slots per TaskManager](#configuring-taskmanager-processing-slots) and `#TMs` are the total number of task managers.

To support, for example, a cluster of 20 8-slot machines, you should use roughly 5000 network
buffers for optimal throughput.

Each network buffer has by default a size of 32 KiBytes. In the example above, the system would thus
allocate roughly 300 MiBytes for network buffers.

The number and size of network buffers can be configured with the following parameters:

- `taskmanager.network.numberOfBuffers`, and
- `taskmanager.memory.segment-size`.

### Configuring Temporary I/O Directories

Although Flink aims to process as much data in main memory as possible, it is not uncommon that more data needs to be processed than memory is available. Flink's runtime is designed to write temporary data to disk to handle these situations.

The `taskmanager.tmp.dirs` parameter specifies a list of directories into which Flink writes temporary files. The paths of the directories need to be separated by ':' (colon character). Flink will concurrently write (or read) one temporary file to (from) each configured directory. This way, temporary I/O can be evenly distributed over multiple independent I/O devices such as hard disks to improve performance. To leverage fast I/O devices (e.g., SSD, RAID, NAS), it is possible to specify a directory multiple times.

If the `taskmanager.tmp.dirs` parameter is not explicitly specified, Flink writes temporary data to the temporary directory of the operating system, such as */tmp* in Linux systems.

### Configuring TaskManager processing slots

Flink executes a program in parallel by splitting it into subtasks and scheduling these subtasks to processing slots.

Each Flink TaskManager provides processing slots in the cluster. The number of slots is typically proportional to the number of available CPU cores __of each__ TaskManager. As a general recommendation, the number of available CPU cores is a good default for `taskmanager.numberOfTaskSlots`.

When starting a Flink application, users can supply the default number of slots to use for that job. The command line value therefore is called `-p` (for parallelism). In addition, it is possible to [set the number of slots in the programming APIs]({{site.baseurl}}/dev/parallel.html) for the whole application and for individual operators.

<img src="{{ site.baseurl }}/fig/slots_parallelism.svg" class="img-responsive" />

{% top %}
