
## Hive优化 - 关键参数配置指导 ##

### 1. Container内存相关 ###
#### 1.1. map的内存大小

**【参数值】**

&emsp;&emsp;mapreduce.map.memory.mb

**【参数解析】**

&emsp;&emsp;map任务的内存限制。

**【如何调优】**

&emsp;&emsp;默认：4096MB

mapreduce.map.memory.mb控制每个map任务的内存大小，默认值为4G，**对于当前任务是内存密集型任务（如ORC文件读取/写入、全局排序）的场景，建议增大内存值，但不能超过yarn.scheduler.maximum-allocation-mb的值。**


#### 1.2. reduce的内存大小

**【参数值】**

&emsp;&emsp;mapreduce.reduce.memory.mb

**【参数解析】**

&emsp;&emsp;reduce任务的内存限制。

**【如何调优】**

&emsp;&emsp;默认：4096MB

mapreduce.reduce.memory.mb控制每个reduce任务的内存大小，默认值为4G，**对于当前任务是内存密集型任务（如ORC文件读取/写入、全局排序）的场景，建议增大内存值，但不能超过yarn.scheduler.maximum-allocation-mb的值。**

#### 1.3. Container的内存大小
**【参数值】**

&emsp;&emsp;yarn.scheduler.maximum-allocation-mb

**【参数解析】**

&emsp;&emsp;ResourceManager中每个container请求分配的最大内存。

**【如何调优】**

&emsp;&emsp;默认：6144MB

yarn.scheduler.maximum-allocation-mb表示ResourceManager给每个container请求分配的最大内存，默认值为6G，该值属于YARN参数，需要在YARN参数配置界面进行配置。

### 2. Container CPU相关
#### 2.1. map的虚拟核数

**【参数值】**

&emsp;&emsp;mapreduce.map.cpu.vcores

**【参数解析】**

&emsp;&emsp;每个map任务需要的CPU虚拟核数。

**【如何调优】**

&emsp;&emsp;默认：1

mapreduce.map.cpu.vcores控制每个map任务需要的CPU核数，默认值为1，对于当前任务是CPU密集型任务（如复杂数学计算）的场景，建议增大该值，但不能超过yarn.scheduler.maximum-allocation-vcores的值。

#### 2.2.	reduce的虚拟核数

**【参数值】**

&emsp;&emsp;mapreduce.reduce.cpu.vcores

**【参数解析】**

&emsp;&emsp;每个reduce任务需要的CPU虚拟核数。

**【如何调优】**

&emsp;&emsp;默认：1

mapreduce.reduce.cpu.vcores控制每个reduce任务需要的CPU核数，默认值为1，对于当前任务是CPU密集型任务（如复杂数学计算）的场景，建议增大该值，但不能超过yarn.scheduler.maximum-allocation-vcores的值。

#### 2.3.	Container的虚拟核数

**【参数值】**

&emsp;&emsp;yarn.scheduler.maximum-allocation-vcores

**【参数解析】**

&emsp;&emsp;ResourceManager为每个container请求的最大虚拟CPU核数。

**【如何调优】**

&emsp;&emsp;默认：32

yarn.scheduler.maximum-allocation-vcores表示ResourceManager中每个container请求的最大虚拟CPU核数，默认值是32，该值属于YARN参数，需要在YARN参数配置界面进行配置。

### 3. Map数量相关
#### 3.1. Map处理的最大数据量

**【参数值】**

&emsp;&emsp;mapreduce.input.fileinputformat.split.maxsize

**【参数解析】**

&emsp;&emsp;map输入信息应被拆分成的数据块的最大大小。

**【如何调优】**

&emsp;&emsp;默认：256000000字节

对于扫描、过滤数据的逻辑比较复杂、输入数据量较大条数较多的场景，建议按照实际情况适当调小该参数的值，如128000000或是64000000，这样由于每个map处理的数据变少，map数增加了，提高了并行计算的力度，整体效率会提升。

#### 3.2.	每个节点Map分片最小值

**【参数值】**

&emsp;&emsp;mapred.min.split.size.per.node

**【参数解析】**

&emsp;&emsp;每个节点的map分片的最小值。

**【如何调优】**

&emsp;&emsp;默认：1字节

对于输入数据文件存在大量小文件的场景，建议分别设置该参数的值为128000000，使map启动前对大量小文件进行合并。

#### 3.3.	每个机架Map分片最小值

**【参数值】**

&emsp;&emsp;mapred.min.split.size.per.rack

**【参数解析】**

&emsp;&emsp;每个机架的map分片的最小值。

**【如何调优】**

&emsp;&emsp;默认：1字节

对于输入数据文件存在大量小文件的场景，建议分别设置该参数的值为128000000，使map启动前对大量小文件进行合并。

### 4.	Reduce数量相关
#### 4.1. 每个reduce任务处理的数据量

**【参数值】**

&emsp;&emsp;hive.exec.reducers.bytes.per.reducer

**【参数解析】**

&emsp;&emsp;每个reduce任务处理的数据量。

**【如何调优】**

&emsp;&emsp;默认：256000000字节

对于关联、聚合、排序时reduce端待处理数据量较大的场景，当发现reduce处理速度很慢时，建议按照实际情况适当调小该参数的值，这样由于每个reduce处理的数据变少，reduce数增加了，提高了并行计算的力度，整体效率会提升。

#### 4.2. 最大reduce数

**【参数值】**

&emsp;&emsp;hive.exec.reducers.max

**【参数解析】**

&emsp;&emsp;每个MapReduce任务能启动的最大的reduce数。

**【如何调优】**

&emsp;&emsp;默认：999 

建议根据集群总体资源情况，以及分配给当前租户的资源情况，设置该参数值为当前租户所能启动的最大的container数。

#### 4.3. reduce任务数量

**【参数值】**

&emsp;&emsp;mapred.reduce.tasks

**【参数解析】**

&emsp;&emsp;MapReduce任务启动的reduce数。

**【如何调优】**

&emsp;&emsp;默认：-1

mapred.reduce.tasks表示reduce任务数量，默认值为-1，表示由Hive根据输入数据量除以hive.exec.reducers.bytes.per.reducer参数的值自动计算reduce的任务数量。

该值的优先级高于根据hive.exec.reducers.bytes.per.reducer参数的自动计算。

对于关联、聚合、排序时reduce端待处理数据量较大的场景，当发现reduce处理速度很慢时，建议按照实际情况调高该参数的值，但不能超过hive.exec.reducers.max的值。

### 5. Hive优化器相关
#### 5.1. 相关性优化开关
**【参数值】**

&emsp;&emsp;hive.optimize.correlation

**【参数解析】**

&emsp;&emsp;是否开启相关性优化。

**【如何调优】**

&emsp;&emsp;默认：false

相关性优化，旨在利用下面两种查询的相关性：

- (a). 输入相关性：在原始operator树中，同一个输入表被多个MapReduce任务同时使用的场景；
- (b). 作业流程的相关性：两个有依赖关系的MapReduce的任务的shuffle方式相同。

相关性优化特性可以减少MR任务个数，从而提升整体效率，建议设置为true。
