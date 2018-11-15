---
title: YARN&MapReduce管理
category: Operations Guide
---


## YARN
### yarn logs 查看日志
```
[root@cvm-da-datasvr-whd1 ~]# yarn logs -help
options parsing failed: Missing required option: applicationId
Retrieve logs for completed YARN applications.
usage: yarn logs -applicationId <application ID> [OPTIONS]

general options are:
 -appOwner <Application Owner>   AppOwner (assumed to be current user if
                                 not specified)
 -containerId <Container ID>     ContainerId (must be specified if node
                                 address is specified)
 -nodeAddress <Node Address>     NodeAddress in the format nodename:port
                                 (must be specified if container id is
                                 specified)
```
1. 注意kerberos的账号是否正确，例如hdfs认证的，就在/tmp/logs/hdfs/logs下搜索，hive认证的就在/tmp/logs/hive/logs下搜索
2. yarn日志是保存在hdfs上的
```
[root@cvm-da-datasvr-whd1 ~]# hadoop dfs -du /tmp/logs/hive/logs
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

196830  590490   /tmp/logs/hive/logs/application_1530181175351_0037
392621  1177863  /tmp/logs/hive/logs/application_1530181175351_0038
381292  1143876  /tmp/logs/hive/logs/application_1533033734314_0082
391927  1175781  /tmp/logs/hive/logs/application_1533033734314_0090
195879  587637   /tmp/logs/hive/logs/application_1533033734314_0095
391321  1173963  /tmp/logs/hive/logs/application_1533033734314_0098
390803  1172409  /tmp/logs/hive/logs/application_1533033734314_0099
392520  1177560  /tmp/logs/hive/logs/application_1533033734314_0101
397605  1192815  /tmp/logs/hive/logs/application_1533033734314_0102
200116  600348   /tmp/logs/hive/logs/application_1533033734314_0103
393102  1179306  /tmp/logs/hive/logs/application_1533033734314_0104
397596  1192788  /tmp/logs/hive/logs/application_1533033734314_0105
243064  729192   /tmp/logs/hive/logs/application_1533033734314_0117
[root@cvm-da-datasvr-whd1 ~]# yarn logs -applicationId application_1533033734314_0117
18/08/07 09:50:55 INFO client.RMProxy: Connecting to ResourceManager at cvm-da-datasvr-whd1/10.60.16.223:8032
/tmp/logs/hdfs/logs/application_1533033734314_0117 does not exist.
Log aggregation has not completed or is not enabled.
[root@cvm-da-datasvr-whd1 ~]# kinit -k -t /etc/presto/hive.keytab hive
[root@cvm-da-datasvr-whd1 ~]# yarn logs -applicationId application_1533033734314_0117
```
