## 7.2 Hive优化思路与常见问题
### 1. 定位调优思路

&emsp;&emsp;**第一步，分析SQL待处理的表及文件**，统计待处理的表的文件数、数据量、条数、文件格式、压缩格式，分析是否有更适合的文件存储格式、压缩格式，是否能够使用上分区或分桶，是否有大量小文件需要map前合并。如果有优化空间，则执行优化。

&emsp;&emsp;**第二步，分析SQL的结构**，是否有重复的子查询可以存到中间表，是否可以使用相关性优化器，是否出现笛卡尔积需要去除，是否可以使用Multiple Insert语句，是否使用了低性能的UDF或SerDe需要替换。如果有优化空间，则执行优化。

&emsp;&emsp;**第三步，分析SQL的操作**，是否可利用MapJoin，是否可使用SMB Join，是否需要设置map端聚合，是否需要优化count(distinct)，是否全局排序可以使用局部排序代替，是否可以使用向量化优化、基于代价的优化等优化器。如果有优化空间，则执行优化。

&emsp;&emsp;**第四步，观察SQL启动的MR运行情况**，如果map运行缓慢，考虑减小Map处理的最大数据量提高并发度，考虑增大map的内存和虚拟核数；如果是reduce运行缓慢，是否有group by倾斜需要解决，是否有join倾斜需要处理，当大量重复数据做去重时减少Reduce数量，当大量匹配记录做关联时增加Reduce数量。

### 2. 日志搜集 
### 2.1. HiveServer日志获取

Hive调优需要看HiveServer的运行日志及GC日志。
HiveServer日志路径为：HiveServer节点的**/var/log/Bigdata/hive/hiveserver/**。

HiveServer运行日志:**hive.log**

HiveServer GC日志:**hive-omm-gc.log.0.current**



### 3. 常见问题处理

#### 3.1. OOM（内存溢出）
##### 3.1.1. 问题描述
HiveServer运行日志及MR日志报以下错误：

```
Container [pid=48338,containerID=container_1417574466181_3484_01_000043] is running beyond physical memory limits. Current usage: 1.0 GB of 1 GB physical memory used; 2.0 GB of 2.1 GB virtual memory used. Killing container. Dump of the process-tree for container_1417574466181_3484_01_000043 : |- PID PPID PGRPID SESSID CMD_NAME USER_MODE_TIME(MILLIS) SYSTEM_TIME(MILLIS) VMEM_USAGE(BYTES) RSSMEM_USAGE(PAGES) FULL_CMD_LINE |- 48345 48338 48338 48338 (java) 2432 61 2062110720 272981
```
##### 3.1.2. 解决方案

从日志可以看出，Container物理内存不够用了，观察YARN界面该container是map还是reduce。

如果是map，则增大mapreduce.map.memory.mb的设置，如设置成6144（默认值为4096），但不能超过yarn.scheduler.maximum-allocation-mb的值。

如果是reduce，则增大mapreduce.reduce.memory.mb的设置，如设置成6144（默认值为4096），但不能超过yarn.scheduler.maximum-allocation-mb的值。

