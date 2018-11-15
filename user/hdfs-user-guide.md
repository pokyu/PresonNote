---
title: HDFS使用指导
category: User Guide
---


## HDFS
### 登录认证
HDFS权限是通过AD账号进行权限控制，第一次使用需要向jessetong(佟博宇)申请账号权限，注：目前还未加入审批流，后序完善。
**命令**：
```
kinit 用户名
```
示例：
```
[dauser@cvm-da-datasvr2-etl2 ~]$ kinit jessetong
Password for jessetong@WESURE.CN:

```
账号密码与域账号密码相同。
使用klist查看是否认证成功，如下结果就是认证成功的。
```
[dauser@cvm-da-datasvr2-etl2 ~]$ klist
Ticket cache: FILE:/tmp/krb5cc_1000
Default principal: jessetong@WESURE.CN

Valid starting       Expires              Service principal
11/15/2018 16:08:23  11/16/2018 02:08:23  krbtgt/WESURE.CN@WESURE.CN
    renew until 11/22/2018 16:08:20
```
如下是没有成功示例
```
[dauser@cvm-da-datasvr2-etl2 ~]$ klist
klist: No credentials cache found (filename: /tmp/krb5cc_1000)
```

注：同时登陆相同OS用户，只需要一人认证。

### HDFS的操作

HDFS的操作类似与Linux命令

语法类似：
```
hadoop dfs + COMMAND
```

#### 查看命令帮助文档
```
hadoop dfs -help
hadoop dfs -command -help
```
上面两条语句查看的命令级别不同。

例如：
| 序号 | Linux命令 | HDFS命令 |
| :---: | --- | --- |
| 1 | ls /path | hadoop dfs -ls /path |
| 2 | mkdir /path | mkdir /path |

#### 查询操作

```
[dauser@cvm-da-datasvr2-etl2 ~]$ hadoop dfs -ls /
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

Found 14 items
drwxrwxrwx   - yarn           hadoop          0 2018-11-09 00:48 /app-logs
drwxr-xr-x   - hdfs           hdfs            0 2018-10-16 20:49 /apps
drwxr-xr-x   - yarn           hadoop          0 2018-10-11 10:27 /ats
drwxr-xr-x   - havixie        hdfs            0 2018-11-14 17:38 /benchmarks
drwxr-xr-x   - public_dataetl hdfs            0 2018-11-12 00:36 /druid
drwxr-xr-x   - hdfs           hdfs            0 2018-10-11 10:27 /hdp
drwx------   - livy           hdfs            0 2018-10-26 15:18 /livy2-recovery
drwxr-xr-x   - mapred         hdfs            0 2018-10-11 10:27 /mapred
drwxrwxrwx   - mapred         hadoop          0 2018-10-11 10:27 /mr-history
drwxr-xr-x   - hdfs           hdfs            0 2018-10-11 10:41 /ranger
drwxrwxrwx   - spark          hadoop          0 2018-11-15 16:12 /spark2-history
drwxr-xr-x   - hdfs           hdfs            0 2018-11-15 16:08 /system
drwxrwxrwx   - hdfs           hdfs            0 2018-11-01 21:39 /tmp
drwxr-xr-x   - hdfs           hdfs            0 2018-11-11 15:48 /user
```

#### 文件操作
创建文件夹：
使用"hadoop dfs -mkdir 路径"来创建文件夹：
```
hadoop dfs -mkdir 路径
```
示例
```
[osuser@dataflow2 ~]$ hadoop dfs -ls /tmp
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

Found 5 items
d---------   - hdfs   supergroup          0 2018-07-18 11:48 /tmp/.cloudera_health_monitoring_canary_files
drwxr-xr-x   - yarn   supergroup          0 2018-06-13 17:20 /tmp/hadoop-yarn
drwx-wx-wx   - hive   supergroup          0 2018-06-07 15:12 /tmp/hive
drwxrwxrwt   - mapred hadoop              0 2018-06-13 17:20 /tmp/logs
drwxr-xr-x   - hive   supergroup          0 2018-07-06 11:47 /tmp/pokyu
[osuser@dataflow2 ~]$ hadoop dfs -mkdir /tmp/jessetong
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

[osuser@dataflow2 ~]$ hadoop dfs -ls /tmp
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

Found 6 items
d---------   - hdfs   supergroup          0 2018-07-18 11:49 /tmp/.cloudera_health_monitoring_canary_files
drwxr-xr-x   - yarn   supergroup          0 2018-06-13 17:20 /tmp/hadoop-yarn
drwx-wx-wx   - hive   supergroup          0 2018-06-07 15:12 /tmp/hive
drwxr-xr-x   - hive   supergroup          0 2018-07-18 11:49 /tmp/jessetong
drwxrwxrwt   - mapred hadoop              0 2018-06-13 17:20 /tmp/logs
drwxr-xr-x   - hive   supergroup          0 2018-07-06 11:47 /tmp/pokyu
[osuser@dataflow2 ~]$ 
```
#### 文件上传
```
hadoop dfs -put 源文本 HDFS上目标路径
```
示例：
```
[osuser@dataflow2 ~]$ hadoop dfs -put ./test.txt /tmp/jessetong/
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

[osuser@dataflow2 ~]$ hadoop dfs -ls /tmp/jessetong/
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

Found 1 items
-rw-r--r--   3 hive supergroup        436 2018-07-18 13:14 /tmp/jessetong/test.txt
```
#### 读取文件内容：
```
hadoop dfs -cat HDFS上文件路径
```
示例：
```
[osuser@dataflow2 ~]$ hadoop dfs -cat /tmp/jessetong/test.txt
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

18/06/19 17:30:24 ERROR yarn.ApplicationMaster: Failed to connect to driver at dataflow2:46261, retrying ...
18/06/19 17:30:24 ERROR yarn.ApplicationMaster: Failed to connect to driver at dataflow2:46261, retrying ...
18/06/19 17:30:24 ERROR yarn.ApplicationMaster: Failed to connect to driver at dataflow2:46261, retrying ...
18/06/19 17:30:24 ERROR yarn.ApplicationMaster: Failed to connect to driver at dataflow2:46261, retrying ...
```

#### 文件加载：
```
hadoop fs -appendToFile ./test2.txt /tmp/jessetong/test.txt
```
示例：
```
[osuser@dataflow2 ~]$ hadoop dfs -appendToFile ./test2.txt /tmp/jessetong/test.txt
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

[osuser@dataflow2 ~]$ hadoop dfs -cat /tmp/jessetong/test.txt
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

18/06/19 17:30:24 ERROR yarn.ApplicationMaster: Failed to connect to driver at dataflow2:46261, retrying ...
18/06/19 17:30:24 ERROR yarn.ApplicationMaster: Failed to connect to driver at dataflow2:46261, retrying ...
18/06/19 17:30:24 ERROR yarn.ApplicationMaster: Failed to connect to driver at dataflow2:46261, retrying ...
18/06/19 17:30:24 ERROR yarn.ApplicationMaster: Failed to connect to driver at dataflow2:46261, retrying ...
append file column 1
append file column 2
```

#### 其它：
```
检查文件：
-e 检查文件是否存在。如果存在则返回0。
-z 检查文件是否是0字节。如果是则返回0。
-d 如果路径是个目录，则返回1，否则返回0。

 hadoop fs -test -e /tmp/20161212/123.txt
```
```
文件删除: hadoop dfs -rm /tmp/20161212/1234.txt
文件删除（跳过回收站）:
 hadoop dfs -rm -skipTrash /tmp/20161212/12345.txt
合并下载：
hadoop fs -getmerge /tmp/20161212 /opt/1212.txt
hadoop fs -get /tmp/20161212/123.txt /opt/1234.txt
```
