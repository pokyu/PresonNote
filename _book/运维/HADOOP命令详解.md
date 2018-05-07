## HADOOP命令详解
### HDFS
#### jps
```
[dauser@hadoop1 current]$ jps
21120 RunJar
24000 NameNode
25426 Jps
22883 RunJar
22395 RunJar
31163 Worker
30988 Master
5436 DFSZKFailoverController
24493 ResourceManager
4878 RunJar
```

#### namespaceID
找namespaceID配置参数的位置：首先，找到hdfs-site.xml文件，查看里面的
```
<!--指定datanode数据存储地址--> 
  <property> 
    <name>dfs.datanode.data.dir</name> 
    <value>file:/data/dauser/storage/hadoop/data</value> 
  </property> 
```
那么/data/dauser/storage/hadoop/data下current目录里，有个VERSION。
```
[dauser@hadoop1 current]$ pwd
/data/dauser/storage/hadoop/data/current
[dauser@hadoop1 current]$ more VERSION
#Mon May 07 20:09:49 CST 2018
storageID=DS-bbd35ca1-bdbf-4d51-afc8-1a7a649107bc
clusterID=CID-159488f1-4bfc-40ee-b661-aa8e9760408c
cTime=0
datanodeUuid=f8c1ff05-110a-40aa-8b0a-648d732af1ed
storageType=DATA_NODE
layoutVersion=-56
```
### MapReduce

#### hadoop job -list
查看MR正在执行的任务
```
hadoop job -list
```
 - **JobId**：
 - **State**：
 - **StartTime**：
 - **UserName**：
 - **Queue**：
 - **Priority**：
 - **UsedContainers**：
 - **RsvdContainers**：
 - **UsedMem**：
 - **RsvdMem**：
 - **NeededMem**：
 - **AM info**：


