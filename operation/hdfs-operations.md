---
title: HDFS管理
category: Operations Guide
---

### 查看系统空间
```
系统大小：hadoop dfs -df -h
文件大小：hadoop dfs -du -h /user/hive/warehouse
目录数情况： hadoop dfs -count /tmp/20161212
```
#### 修改副本数量：
```
hadoop dfs -setrep [-r] [-w] <副本数> <路径> 修改副本数量
```
示例：
hadoop fs -setrep 2 /tmp/jessetong/test.txt 将/tmp/jessetong/test.txt从三副本改成两副本。
```
[osuser@dataflow2 ~]$ hadoop fsck /tmp/jessetong/test.txt
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

Connecting to namenode via http://cvm-da-datasvr-whd1:50070/fsck?ugi=hive&path=%2Ftmp%2Fjessetong%2Ftest.txt
FSCK started by hive (auth:KERBEROS_SSL) from /10.60.16.54 for path /tmp/jessetong/test.txt at Wed Jul 18 14:11:05 CST 2018
.Status: HEALTHY
 Total size:    520 B
 Total dirs:    0
 Total files:   1
 Total symlinks:        0
 Total blocks (validated):      1 (avg. block size 520 B)
 Minimally replicated blocks:   1 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:        0
 Missing replicas:      0 (0.0 %)
 Number of data-nodes:          5
 Number of racks:       1
FSCK ended at Wed Jul 18 14:11:05 CST 2018 in 2 milliseconds


The filesystem under path '/tmp/jessetong/test.txt' is HEALTHY
[osuser@dataflow2 ~]$ hadoop fs -setrep 2 /tmp/jessetong/test.txt
Replication 2 set: /tmp/jessetong/test.txt
[osuser@dataflow2 ~]$ hadoop fsck /tmp/jessetong/test.txt
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

Connecting to namenode via http://cvm-da-datasvr-whd1:50070/fsck?ugi=hive&path=%2Ftmp%2Fjessetong%2Ftest.txt
FSCK started by hive (auth:KERBEROS_SSL) from /10.60.16.54 for path /tmp/jessetong/test.txt at Wed Jul 18 14:11:59 CST 2018
.Status: HEALTHY
 Total size:    520 B
 Total dirs:    0
 Total files:   1
 Total symlinks:        0
 Total blocks (validated):      1 (avg. block size 520 B)
 Minimally replicated blocks:   1 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     2.0
 Corrupt blocks:        0
 Missing replicas:      0 (0.0 %)
 Number of data-nodes:          5
 Number of racks:       1
FSCK ended at Wed Jul 18 14:11:59 CST 2018 in 0 milliseconds


The filesystem under path '/tmp/jessetong/test.txt' is HEALTHY
```



### 查看HDFS上block的信息

查看文件中损坏的块（-list-corruptfileblocks）
将损坏的文件移动至/lost+found目录（-move）
删除损坏的文件（-delete）
检查并打印正在被打开执行写操作的文件（-openforwrite）

```
[root@cvm-da-datasvr-whd4 parcel-cache]# hdfs fsck /tmp/pokyu/test.file
Connecting to namenode via http://cvm-da-datasvr-whd1:50070/fsck?ugi=hive&path=%2Ftmp%2Fpokyu%2Ftest.file
FSCK started by hive (auth:KERBEROS_SSL) from /10.60.16.78 for path /tmp/pokyu/test.file at Mon Jun 25 15:36:24 CST 2018
.Status: HEALTHY
 Total size:    2113779811 B
 Total dirs:    0
 Total files:   1
 Total symlinks:                0
 Total blocks (validated):      16 (avg. block size 132111238 B)
 Minimally replicated blocks:   16 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          5
 Number of racks:               1
FSCK ended at Mon Jun 25 15:36:24 CST 2018 in 0 milliseconds
```
#### 检查并列出所有文件状态（-files）
```
[root@cvm-da-datasvr-whd4 parcel-cache]# hdfs fsck /tmp/pokyu/test.file -files
Connecting to namenode via http://cvm-da-datasvr-whd1:50070/fsck?ugi=hive&files=1&path=%2Ftmp%2Fpokyu%2Ftest.file
FSCK started by hive (auth:KERBEROS_SSL) from /10.60.16.78 for path /tmp/pokyu/test.file at Mon Jun 25 15:38:24 CST 2018
/tmp/pokyu/test.file 2113779811 bytes, 16 block(s):  OK
Status: HEALTHY
 Total size:    2113779811 B
 Total dirs:    0
 Total files:   1
 Total symlinks:                0
 Total blocks (validated):      16 (avg. block size 132111238 B)
 Minimally replicated blocks:   16 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          5
 Number of racks:               1
FSCK ended at Mon Jun 25 15:38:24 CST 2018 in 1 milliseconds


The filesystem under path '/tmp/pokyu/test.file' is HEALTHY
```
#### 打印文件的Block报告（-blocks）
需要和-files一起使用。
```
[root@cvm-da-datasvr-whd4 parcel-cache]# hdfs fsck /tmp/pokyu/test.file -files -blocks
Connecting to namenode via http://cvm-da-datasvr-whd1:50070/fsck?ugi=hive&files=1&blocks=1&path=%2Ftmp%2Fpokyu%2Ftest.file
FSCK started by hive (auth:KERBEROS_SSL) from /10.60.16.78 for path /tmp/pokyu/test.file at Mon Jun 25 15:38:55 CST 2018
/tmp/pokyu/test.file 2113779811 bytes, 16 block(s):  OK
0. BP-1402901901-10.60.16.223-1528283076406:blk_1073769410_28587 len=134217728 Live_repl=3
1. BP-1402901901-10.60.16.223-1528283076406:blk_1073769411_28588 len=134217728 Live_repl=3
2. BP-1402901901-10.60.16.223-1528283076406:blk_1073769412_28589 len=134217728 Live_repl=3
3. BP-1402901901-10.60.16.223-1528283076406:blk_1073769413_28590 len=134217728 Live_repl=3
4. BP-1402901901-10.60.16.223-1528283076406:blk_1073769414_28591 len=134217728 Live_repl=3
5. BP-1402901901-10.60.16.223-1528283076406:blk_1073769415_28592 len=134217728 Live_repl=3
6. BP-1402901901-10.60.16.223-1528283076406:blk_1073769416_28593 len=134217728 Live_repl=3
7. BP-1402901901-10.60.16.223-1528283076406:blk_1073769417_28594 len=134217728 Live_repl=3
8. BP-1402901901-10.60.16.223-1528283076406:blk_1073769418_28595 len=134217728 Live_repl=3
9. BP-1402901901-10.60.16.223-1528283076406:blk_1073769419_28596 len=134217728 Live_repl=3
10. BP-1402901901-10.60.16.223-1528283076406:blk_1073769420_28597 len=134217728 Live_repl=3
11. BP-1402901901-10.60.16.223-1528283076406:blk_1073769421_28598 len=134217728 Live_repl=3
12. BP-1402901901-10.60.16.223-1528283076406:blk_1073769422_28599 len=134217728 Live_repl=3
13. BP-1402901901-10.60.16.223-1528283076406:blk_1073769423_28600 len=134217728 Live_repl=3
14. BP-1402901901-10.60.16.223-1528283076406:blk_1073769424_28601 len=134217728 Live_repl=3
15. BP-1402901901-10.60.16.223-1528283076406:blk_1073769425_28602 len=100513891 Live_repl=3

Status: HEALTHY
 Total size:    2113779811 B
 Total dirs:    0
 Total files:   1
 Total symlinks:                0
 Total blocks (validated):      16 (avg. block size 132111238 B)
 Minimally replicated blocks:   16 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          5
 Number of racks:               1
FSCK ended at Mon Jun 25 15:38:55 CST 2018 in 0 milliseconds


The filesystem under path '/tmp/pokyu/test.file' is HEALTHY

```
其中，/tmp/pokyu/test.file 2113779811 bytes, 16 block(s):  OK: 表示文件的总大小和block数；

0. BP-1402901901-10.60.16.223-1528283076406:blk_1073769410_28587 len=134217728 Live_repl=3
1. BP-1402901901-10.60.16.223-1528283076406:blk_1073769411_28588 len=134217728 Live_repl=3
2. BP-1402901901-10.60.16.223-1528283076406:blk_1073769412_28589 len=134217728 Live_repl=3
... ...
前面的0. 1. 2.代表该文件的block索引，16的文件块，就从0-15;

BP-1402901901-10.60.16.223-1528283076406:blk_1073769410_28587表示block id；

len=134217728 表示该文件块大小；

repl=3 表示该文件块副本数；


####　打印文件块的位置信息（-locations）
需要和-files -blocks一起使用。

```
[root@cvm-da-datasvr-whd4 cloudera]# hdfs fsck /tmp/pokyu/test.file -files -blocks -locations
Connecting to namenode via http://cvm-da-datasvr-whd1:50070/fsck?ugi=hive&files=1&blocks=1&locations=1&path=%2Ftmp%2Fpokyu%2Ftest.file
FSCK started by hive (auth:KERBEROS_SSL) from /10.60.16.78 for path /tmp/pokyu/test.file at Mon Jun 25 15:46:09 CST 2018
/tmp/pokyu/test.file 2113779811 bytes, 16 block(s):  OK
0. BP-1402901901-10.60.16.223-1528283076406:blk_1073769410_28587 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.193:1004,DS-f36f3aa4-ff41-454e-9ba0-c54d9f3825f1,DISK]]
1. BP-1402901901-10.60.16.223-1528283076406:blk_1073769411_28588 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.193:1004,DS-f36f3aa4-ff41-454e-9ba0-c54d9f3825f1,DISK], DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK]]
2. BP-1402901901-10.60.16.223-1528283076406:blk_1073769412_28589 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.193:1004,DS-f36f3aa4-ff41-454e-9ba0-c54d9f3825f1,DISK], DatanodeInfoWithStorage[10.60.16.78:1004,DS-09c060fe-9b6f-4d9b-a33e-51f40add5aa5,DISK]]
3. BP-1402901901-10.60.16.223-1528283076406:blk_1073769413_28590 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.78:1004,DS-09c060fe-9b6f-4d9b-a33e-51f40add5aa5,DISK], DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK]]
4. BP-1402901901-10.60.16.223-1528283076406:blk_1073769414_28591 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.193:1004,DS-f36f3aa4-ff41-454e-9ba0-c54d9f3825f1,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK]]
5. BP-1402901901-10.60.16.223-1528283076406:blk_1073769415_28592 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK], DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK], DatanodeInfoWithStorage[10.60.16.78:1004,DS-09c060fe-9b6f-4d9b-a33e-51f40add5aa5,DISK]]
6. BP-1402901901-10.60.16.223-1528283076406:blk_1073769416_28593 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.78:1004,DS-09c060fe-9b6f-4d9b-a33e-51f40add5aa5,DISK]]
7. BP-1402901901-10.60.16.223-1528283076406:blk_1073769417_28594 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.193:1004,DS-f36f3aa4-ff41-454e-9ba0-c54d9f3825f1,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK]]
8. BP-1402901901-10.60.16.223-1528283076406:blk_1073769418_28595 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK], DatanodeInfoWithStorage[10.60.16.78:1004,DS-09c060fe-9b6f-4d9b-a33e-51f40add5aa5,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK]]
9. BP-1402901901-10.60.16.223-1528283076406:blk_1073769419_28596 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK], DatanodeInfoWithStorage[10.60.16.193:1004,DS-f36f3aa4-ff41-454e-9ba0-c54d9f3825f1,DISK], DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK]]
10. BP-1402901901-10.60.16.223-1528283076406:blk_1073769420_28597 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK], DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK]]
11. BP-1402901901-10.60.16.223-1528283076406:blk_1073769421_28598 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.193:1004,DS-f36f3aa4-ff41-454e-9ba0-c54d9f3825f1,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK]]
12. BP-1402901901-10.60.16.223-1528283076406:blk_1073769422_28599 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK], DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK], DatanodeInfoWithStorage[10.60.16.78:1004,DS-09c060fe-9b6f-4d9b-a33e-51f40add5aa5,DISK]]
13. BP-1402901901-10.60.16.223-1528283076406:blk_1073769423_28600 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.78:1004,DS-09c060fe-9b6f-4d9b-a33e-51f40add5aa5,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK]]
14. BP-1402901901-10.60.16.223-1528283076406:blk_1073769424_28601 len=134217728 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK], DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK]]
15. BP-1402901901-10.60.16.223-1528283076406:blk_1073769425_28602 len=100513891 Live_repl=3 [DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.42:1004,DS-e0b167b6-52d7-4bde-bb6a-8e5166650153,DISK]]

Status: HEALTHY
 Total size:    2113779811 B
 Total dirs:    0
 Total files:   1
 Total symlinks:                0
 Total blocks (validated):      16 (avg. block size 132111238 B)
 Minimally replicated blocks:   16 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          5
 Number of racks:               1
FSCK ended at Mon Jun 25 15:46:09 CST 2018 in 1 milliseconds


The filesystem under path '/tmp/pokyu/test.file' is HEALTHY
```
和打印出的文件块信息相比，多了一个文件块的位置信息：
[DatanodeInfoWithStorage[10.60.16.107:1004,DS-45c4c4d1-64d3-4909-b126-35bf33315592,DISK], DatanodeInfoWithStorage[10.60.16.123:1004,DS-4b2f3537-dae9-470a-b3fa-53dc80ddf0a9,DISK], DatanodeInfoWithStorage[10.60.16.193:1004,DS-f36f3aa4-ff41-454e-9ba0-c54d9f3825f1,DISK]]


#### 打印文件块位置所在的机架信息（-racks）

```
[root@cvm-da-datasvr-whd4 cloudera]# hdfs fsck /tmp/pokyu/test.file -files -blocks -locations -racks
Connecting to namenode via http://cvm-da-datasvr-whd1:50070/fsck?ugi=hive&files=1&blocks=1&locations=1&racks=1&path=%2Ftmp%2Fpokyu%2Ftest.file
FSCK started by hive (auth:KERBEROS_SSL) from /10.60.16.78 for path /tmp/pokyu/test.file at Mon Jun 25 15:50:58 CST 2018
/tmp/pokyu/test.file 2113779811 bytes, 16 block(s):  OK
0. BP-1402901901-10.60.16.223-1528283076406:blk_1073769410_28587 len=134217728 Live_repl=3 [/default/10.60.16.107:1004, /default/10.60.16.123:1004, /default/10.60.16.193:1004]
1. BP-1402901901-10.60.16.223-1528283076406:blk_1073769411_28588 len=134217728 Live_repl=3 [/default/10.60.16.193:1004, /default/10.60.16.42:1004, /default/10.60.16.123:1004]
2. BP-1402901901-10.60.16.223-1528283076406:blk_1073769412_28589 len=134217728 Live_repl=3 [/default/10.60.16.123:1004, /default/10.60.16.193:1004, /default/10.60.16.78:1004]
3. BP-1402901901-10.60.16.223-1528283076406:blk_1073769413_28590 len=134217728 Live_repl=3 [/default/10.60.16.78:1004, /default/10.60.16.107:1004, /default/10.60.16.123:1004]
4. BP-1402901901-10.60.16.223-1528283076406:blk_1073769414_28591 len=134217728 Live_repl=3 [/default/10.60.16.193:1004, /default/10.60.16.123:1004, /default/10.60.16.107:1004]
5. BP-1402901901-10.60.16.223-1528283076406:blk_1073769415_28592 len=134217728 Live_repl=3 [/default/10.60.16.42:1004, /default/10.60.16.107:1004, /default/10.60.16.78:1004]
6. BP-1402901901-10.60.16.223-1528283076406:blk_1073769416_28593 len=134217728 Live_repl=3 [/default/10.60.16.107:1004, /default/10.60.16.123:1004, /default/10.60.16.78:1004]
7. BP-1402901901-10.60.16.223-1528283076406:blk_1073769417_28594 len=134217728 Live_repl=3 [/default/10.60.16.193:1004, /default/10.60.16.123:1004, /default/10.60.16.42:1004]
8. BP-1402901901-10.60.16.223-1528283076406:blk_1073769418_28595 len=134217728 Live_repl=3 [/default/10.60.16.42:1004, /default/10.60.16.78:1004, /default/10.60.16.123:1004]
9. BP-1402901901-10.60.16.223-1528283076406:blk_1073769419_28596 len=134217728 Live_repl=3 [/default/10.60.16.42:1004, /default/10.60.16.193:1004, /default/10.60.16.107:1004]
10. BP-1402901901-10.60.16.223-1528283076406:blk_1073769420_28597 len=134217728 Live_repl=3 [/default/10.60.16.123:1004, /default/10.60.16.42:1004, /default/10.60.16.107:1004]
11. BP-1402901901-10.60.16.223-1528283076406:blk_1073769421_28598 len=134217728 Live_repl=3 [/default/10.60.16.193:1004, /default/10.60.16.123:1004, /default/10.60.16.42:1004]
12. BP-1402901901-10.60.16.223-1528283076406:blk_1073769422_28599 len=134217728 Live_repl=3 [/default/10.60.16.42:1004, /default/10.60.16.107:1004, /default/10.60.16.78:1004]
13. BP-1402901901-10.60.16.223-1528283076406:blk_1073769423_28600 len=134217728 Live_repl=3 [/default/10.60.16.78:1004, /default/10.60.16.123:1004, /default/10.60.16.107:1004]
14. BP-1402901901-10.60.16.223-1528283076406:blk_1073769424_28601 len=134217728 Live_repl=3 [/default/10.60.16.42:1004, /default/10.60.16.107:1004, /default/10.60.16.123:1004]
15. BP-1402901901-10.60.16.223-1528283076406:blk_1073769425_28602 len=100513891 Live_repl=3 [/default/10.60.16.107:1004, /default/10.60.16.123:1004, /default/10.60.16.42:1004]

Status: HEALTHY
 Total size:    2113779811 B
 Total dirs:    0
 Total files:   1
 Total symlinks:                0
 Total blocks (validated):      16 (avg. block size 132111238 B)
 Minimally replicated blocks:   16 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          5
 Number of racks:               1
FSCK ended at Mon Jun 25 15:50:58 CST 2018 in 1 milliseconds


The filesystem under path '/tmp/pokyu/test.file' is HEALTHY
```
和前面打印出的信息相比，多了机架信息：[/default/10.60.16.107:1004, /default/10.60.16.123:1004, /default/10.60.16.193:1004]


### Fix Under-replicated blocks in HDFS manually

```
su - <$hdfs_user>
 
bash-4.1$ hdfs fsck / | grep 'Under replicated' | awk -F':' '{print $1}' >> /tmp/under_replicated_files 
 
-bash-4.1$ for hdfsfile in `cat /tmp/under_replicated_files`; do echo "Fixing $hdfsfile :" ;  hadoop fs -setrep 3 $hdfsfile; done
```

### How to find out all files with replication factor 1 in HDFS

The **hdfs dfsadmin -report** shows there are blocks with replication factor 1:
```
Missing blocks (with replication factor 1): 1420
```
How to find them out?

You can run **hdfs fcsk** to list all files with their replication counts and grep those with replication factor 1. Run the following command as a HDFS superuser:

```
$ hdfs fsck / -files -blocks -racks |grep relp=1
```

**note:**

When uploading a file by the **hdfs dfs -put** command line in HDFS, how to set a replication factor instead of the global one for the file?

The replication factor of file to be put by hdfs dfs -put is from the property **dfs.replication** from hdfs-site.xml.

The **hdfs** command allows you to overwrite the properties by the **-D** option.

Hence, to save a file by only one replica, you can use the command as follows:
```
hdfs dfs -Ddfs.replication=1 -put /path/to/local/file /path/to/hdfs/dir
```

### Analyze result of the hdfs dfs -report 

### salve
```
Name: 10.60.16.90:1019 (whd16.wesure.cn)
Hostname: whd16.wesure.cn
Decommission Status : Normal
Configured Capacity: 4200714520576 (3.82 TB)
DFS Used: 1315538706540 (1.20 TB)
Non DFS Used: 1312853460884 (1.19 TB)
DFS Remaining: 1357284484179 (1.23 TB)
DFS Used%: 31.32%
DFS Remaining%: 32.31%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 40
Last contact: Wed Nov 14 14:11:56 CST 2018
Last Block Report: Wed Nov 14 12:34:50 CST 2018
```

DFS Used% + DFS Remaining% = 31.32% + 32.31% 没到100%,是因为剩余的一些空间被Non DFS（非Hadoop）的文件占据

Xceivers是指datanode当前用于传输数据的线程数（就是在Datanode处理数据管道写时的那些线程，代码中默认最大值单台Datanode 4096）。
 

## Fix under-replicated blocks

```
hdfs fsck / | grep 'Under replicated' | awk -F':' '{print $1}' >> /tmp/under_replicated_files
for hdfsfile in `cat /tmp/under_replicated_files`; do echo "Fixing $hdfsfile :" ;  hadoop fs -setrep 3 $hdfsfile; done
```

