

## 7.1 Hive调优的目标、原则及手段 ##

### 调优目标 ###
Hive调优的目标是在**不影响其他业务正常运行的前提下，最大限度利用集群的物理资源**，如CPU、内存、磁盘IO，使其某一项达到瓶颈。

### 调优原则 ###

1. 保证map扫描的数据量尽量少

	减少map端扫描数量，需要控制待处理的表文件或中间文件的数据量尽量少。

	优化的方式如：**Hive表文件使用高效的文件格式、Hive表文件使用合适的文件压缩格式、中间文件使用合适的文件压缩格式、利用列裁剪、利用分区裁剪、使用分桶。**

2. 保证map传送给reduce的数据量尽量小

	控制map传送给reduce的数据量，是指**JOIN避免笛卡尔积、启动谓词下推、开启map端聚合功能。**

3. 保证map和reduce处理的数据量尽量均衡

	保证map处理的数据量尽量均衡，是指**使用Hive合并输入格式、必要时对小文件进行合并**。

	保证reduce处理的数据量尽量均衡，是指**解决数据倾斜问题**。包括**解决group by造成的数据倾斜、解决join造成的数据倾斜**。

4. 合理调整map和reduce占用的计算资源

	合理调整map和reduce占用的计算资源，是指**通过参数设置合理调整map和reduce的内存及虚拟核数**。

	根据集群总体资源情况，以及分配给当前租户的资源情况，在不影响其他业务正常运行的条件下，最大限度地利用可使用的计算资源。

5. 合理调整map和reduce的数量

	合理调整map数，是指**通过设置每个map处理数据量的最大和最小值来合理控制map的数量**。

	合理调整reduce数，是指**通过直接设置reduce数量或通过设置每个reduce的处理数据量来合理控制reduce的数量**。

6. 重用计算结果

	重用计算结果，是指将**重复的子查询结果保存到中间表**，供其他查询使用，减少重复计算，节省计算资源。

7. 使用稳定成熟的Hive优化特性

	使用稳定成熟的Hive优化特性，包括：**相关性优化器（Correlation Optimizer），基于代价的优化（Cost-based optimization），向量化查询引擎（Vectorized Query Execution），Join相关优化（Map Join、SMB Join），Multiple Insert特性，TABLESAMPLE抽样查询、Limit优化、局部排序（SORT BY、 DISTRIBUTE BY）**。

8. 使用高效HQL或改用MR

	使用高效HQL，包括**慎用低性能的UDF和SerDe、优化count(distinct)**。

	对于使用HQL比较冗余同时性能低下的场景，在充分理解业务需求后，**改用MR**效率更高。

### 调优手段 ###

1. 利用列裁剪

	当待查询的表字段较多时，选取需要使用的字段进行查询，避免直接select *出大表的所有字段，以免当使用Beeline查询时控制台输出缓冲区被大数据量撑爆。

2. JOIN避免笛卡尔积

	**JOIN场景应严格避免出现笛卡尔积的情况**。参与笛卡尔积JOIN的两个表，交叉关联后的数据条数是两个原表记录数之积，对于JOIN后还有聚合的场景而言，会导致reduce端处理的数据量暴增，极大地影响运行效率。

3. 启动谓词下推

	谓词下推（Predicate Pushdown）是一个逻辑优化：尽早的对底层数据进行过滤以减少后续需要处理的数据量。通过以下参数启动谓词下推。

	![启动谓词下推](/../image/hive/1_1.png "启动谓词下推")

4. 开启Map端聚合功能

	在map中会做部分聚集操作，能够使map传送给reduce的数据量大大减少，从而在一定程度上减轻group by带来的数据倾斜。通过以下参数开启map端聚合功能。

	![开启map端聚合功能](/../image/hive/1_2.png "开启map端聚合功能")

5. 使用Hive合并输入格式

	设置Hive合并输入格式，使Hive在执行map前进行文件合并，使得本轮map处理数据量均衡。通过以下参数设置Hive合并输入格式。

	![设置Hive合并输入格式](/../image/hive/1_3.png "设置Hive合并输入格式")

6. 合并小文件

	启动较多的map或reduce能够提高并发度，加快任务运行速度；但同时在HDFS上生成的文件数目也会越来越多，给HDFS的NameNode造成内存上压力，进而影响HDFS读写效率。

	对于集群的小文件（主要由Hive启动的MR生成）过多已造成NameNode压力时，建议在Hive启动的MR中启动小文件合并。

	小文件合并能够使本轮map输出及整个任务输出的文件完成合并，保证下轮MapReduce任务map处理数据量均衡。

7. 解决group by造成的数据倾斜

	通过开启group by倾斜优化开关，解决group by数据倾斜问题。

	开启优化开关后group by会启动两个MR。第一个 MR Job 中，Map 的输出结果集合会随机分布到 Reduce 中，每个Reduce做部分聚合操作，并输出结果，这样处理的结果是相同的Group By Key有可能被分发到不同的Reduce中，从而达到负载均衡的目的；第二个MR Job再根据预处理的数据结果按照Group By Key分布到Reduce中（这个过程可以保证相同的Group By Key被分布到同一个Reduce中），最后完成最终的聚合操作。

8. 解决Join造成的数据倾斜
	
	两个表关联键的数据分布倾斜，会形成Skew Join。

	解决方案是将这类倾斜的特殊值（记录数超过hive.skewjoin.key参数值）不落入reduce计算，而是先写入HDFS，然后再启动一轮MapJoin专门做这类特殊值的计算，期望能提高计算这部分值的处理速度。设置以下参数。

	![解决Join造成的数据倾斜](/../image/hive/1_4.png "解决Join造成的数据倾斜")

9. 合理调整map和reduce的内存及虚拟核数

	map和reduce的内存及虚拟核数设置，决定了集群资源所能同时启动的container个数，影响集群并行计算的能力。

	对于当前任务是CPU密集型任务（如复杂数学计算）的场景：在map和reduce的虚拟核数默认值基础上，逐渐增大虚拟核数进行调试（mapreduce.map.cpu.vcores和mapreduce.reduce.cpu.vcores参数控制），但不要超过可分配给container的虚拟核数（yarn.nodemanager.resource.cpu-vcores参数控制）。

	对于当前任务是内存密集型任务（如ORC文件读取/写入、全局排序）的场景：在map和reduce的内存默认值基础上，逐渐增大内存值进行调试（mapreduce.map.memory.mb和mapreduce.reduce.memory.mb参数控制），但不要超过当前NodeManager上可运行的所有容器的物理内存总大小（yarn.nodemanager.resource.memory-mb参数控制）。

10. 合理控制map的数量

	map的数量会影响MapReduce扫描、过滤数据的效率。
	
	**对于扫描、过滤数据的逻辑比较复杂、输入数据量较大条数较多的场景**：根据集群总体资源情况，以及分配给当前租户的资源情况，在不影响其他业务正常运行的条件下，map数量需要适当增大，增加并行处理的力度。

11. 合理控制reduce的数量

	reduce数量会影响MapReduce过滤、聚合、对数据排序的效率。

	**对于关联、聚合、排序时reduce端待处理数据量较大的场景**：首先根据每个reduce处理的合适数据量控制reduce的个数，如果每个reduce处理数据仍然很慢，再考虑设置参数增大reduce个数。另一方面，控制能启动的reduce最大个数为分配给当前租户的资源上限，以免影响其他业务的正常运行。

12. 将重复的子查询结果保存到中间表

	**对于指标计算类型的业务场景**，多个指标的HQL语句中可能存在相同的子查询，为避免重复计算浪费计算资源，考虑将重复的子查询的计算结果保存到中间表，实现计算一次、结果共享的优化目标。

13. 启用相关性优化器
	
	相关性优化，旨在利用下面两种查询的相关性：
	- (a)输入相关性：在原始operator树中，同一个输入表被多个MapReduce任务同时使用的场景；
	- (b)作业流程的相关性：两个有依赖关系的MapReduce的任务的shuffle方式相同。

    通过以下参数启用相关性优化：
	![启用相关性优化器](/../image/hive/1_5.png "启用相关性优化器")

	相关参考：
	https://cwiki.apache.org/confluence/display/Hive/Correlation+Optimizer

14. 启用基于代价的优化

	基于代价的优化器，可以基于代价（包括FS读写、CPU、IO等）对查询计划进行进一步的优化选择，提升Hive查询的响应速度。

	通过以下参数启用基于代价的优化：
	![启用基于代价的优化](/../image/hive/1_6.png "启用基于代价的优化")

	相关参考：
	https://cwiki.apache.org/confluence/display/Hive/Cost-based+optimization+in+Hive

15. 启用向量化查询引擎

	传统方式中，对数据的处理是以行为单位，依次处理的。Hive也采用了这种方案。这种方案带来的问题是，针对每一行数据，都要进行数据解析，条件判断，方法调用等操作，从而导致了低效的CPU利用。

	向量化特性，通过每次处理1024行数据，列方式处理，从而减少了方法调用，降低了CPU消耗，提高了CPU利用率。结合JDK1.8对SIMD的支持，获得了极高的性能提升。

	通过以下参数启用向量化查询引擎：
	![启用向量化查询引擎](/../image/hive/1_7.png "启用向量化查询引擎化")
 
	相关参考：
	https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution

16. 启用Join相关优化
	- (a)使用MapJoin。MapJoin是针对以下场景进行的优化：两个待连接表中，有一个表非常大，而另一个表非常小，以至于小表可以直接存放到内存中。这样小表复制多份，在每个map task内存中存在一份（比如存放到hash table中），然后只扫描大表。对于大表中的每一条记录key/value，在hash table中查找是否有相同的key的记录，如果有，则连接后输出即可。
	- (b)使用SMB  Join。
	
	相关参考：
	https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization

17. 使用Multiple Insert特性
    
	以下左图为普通insert，右图为Multiple Insert，减少了MR个数，提升了效率。
	![sql示例](/../image/hive/1_8.png "sql示例")


18. 使用TABLESAMPLE取样查询
	
	在Hive中提供了数据取样（SAMPLING）的功能，用来从Hive表中根据一定的规则进行数据取样，Hive中的数据取样支持数据块取样和分桶表取样。

    以下左图为数据块取样，右图为分桶表取样：
	![sql示例](/../image/hive/1_9.png "sql示例")

19. 启用Limit优化
	
	启用limit优化后，使用limit不再是全表查出，而是抽样查询。涉及参数如下：
	![启用Limit优化](/../image/hive/1_10.png "启用Limit优化")

20. 利用局部排序
	
	Hive中使用order by完成全局排序，正常情况下，order by所启动的MR仅有一个reducer，这使得大数据量的表在全局排序时非常低效和耗时。

	**当全局排序为非必须的场景时**，可以使用sort by在每个reducer范围进行内部排序。同时可以使用distribute by控制每行记录分配到哪个reducer。

21. 慎用低性能的UDF和SerDe

	慎用低性能的UDF和SerDe，**主要指谨慎使用正则表达式类型的UDF和SerDe**。如：regexp、regexp_extract、regexp_replace、rlike、RegexSerDe。
	
	当待处理表的条数很多时，如上亿条，采用诸如([^ ]*)([^ ]*)([^ ]*)(.?)(\".*?\")(-|[0-9]*)(-|[0-9]*)(\".*?\")(\".*?\")这种复杂类型的正则表达式组成过滤条件去匹配记录，会严重地影响map阶段的过滤速度。

	建议在充分理解业务需求后，自行编写更高效准确的UDF实现相应的功能。

22. 优化count(distinct)

	优化方式如下，左图为原始HQL，右图为优化后HQL。
	![sql示例](/../image/hive/1_11.png "sql示例")

20. 改用MR实现

	在某些场景下，直接编写MR比使用HQL更加高效。
