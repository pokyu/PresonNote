## HDFS分析方法 -- hdfs dfsadmin

### hdfs dfsadmin -report
```
[root@CNSZ431014 ~]# hdfs dfsadmin -report
Configured Capacity: 74426056298496 (67.69 TB)
Present Capacity: 70124136352340 (63.78 TB)
DFS Remaining: 33715992505225 (30.66 TB)
DFS Used: 36408143847115 (33.11 TB)
DFS Used%: 51.92%
Under replicated blocks: 29904
Blocks with corrupt replicas: 0
Missing blocks: 0
Missing blocks (with replication factor 1): 0

-------------------------------------------------
Live datanodes (6):

Name: 10.14.18.207:25009 (CNSZ431018)
Hostname: CNSZ431018
Rack: /default/rack0
Decommission Status : Normal
Configured Capacity: 17720489594880 (16.12 TB)
DFS Used: 7213844099821 (6.56 TB)
Non DFS Used: 1017260168087 (947.40 GB)
DFS Remaining: 9489385326972 (8.63 TB)
DFS Used%: 40.71%
DFS Remaining%: 53.55%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 18
Last contact: Mon Dec 11 10:59:04 CST 2017


Name: 10.14.18.208:25009 (CNSZ431019)
Hostname: CNSZ431019
Rack: /default/rack0
Decommission Status : Normal
Configured Capacity: 17720489594880 (16.12 TB)
DFS Used: 7133852738211 (6.49 TB)
Non DFS Used: 1016951088951 (947.11 GB)
DFS Remaining: 9569685767718 (8.70 TB)
DFS Used%: 40.26%
DFS Remaining%: 54.00%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 16
Last contact: Mon Dec 11 10:59:04 CST 2017


Name: 10.14.18.206:25009 (CNSZ431017)
Hostname: CNSZ431017
Rack: /default/rack0
Decommission Status : Normal
Configured Capacity: 17720489594880 (16.12 TB)
DFS Used: 6728401972418 (6.12 TB)
Non DFS Used: 1018018554490 (948.10 GB)
DFS Remaining: 9974069067972 (9.07 TB)
DFS Used%: 37.97%
DFS Remaining%: 56.29%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 19
Last contact: Mon Dec 11 10:59:04 CST 2017


Name: 10.14.18.203:25009 (CNSZ431014)
Hostname: CNSZ431014
Rack: /default/rack0
Decommission Status : Normal
Configured Capacity: 7088195837952 (6.45 TB)
DFS Used: 5117967231685 (4.65 TB)
Non DFS Used: 416702126051 (388.08 GB)
DFS Remaining: 1553526480216 (1.41 TB)
DFS Used%: 72.20%
DFS Remaining%: 21.92%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 23
Last contact: Mon Dec 11 10:59:04 CST 2017


Name: 10.14.18.204:25009 (CNSZ431015)
Hostname: CNSZ431015
Rack: /default/rack0
Decommission Status : Normal
Configured Capacity: 7088195837952 (6.45 TB)
DFS Used: 4596038181902 (4.18 TB)
Non DFS Used: 417036831431 (388.40 GB)
DFS Remaining: 2075120824619 (1.89 TB)
DFS Used%: 64.84%
DFS Remaining%: 29.28%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 22
Last contact: Mon Dec 11 10:59:03 CST 2017


Name: 10.14.18.205:25009 (CNSZ431016)
Hostname: CNSZ431016
Rack: /default/rack0
Decommission Status : Normal
Configured Capacity: 7088195837952 (6.45 TB)
DFS Used: 5618039623078 (5.11 TB)
Non DFS Used: 415951177146 (387.38 GB)
DFS Remaining: 1054205037728 (981.80 GB)
DFS Used%: 79.26%
DFS Remaining%: 14.87%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 28
Last contact: Mon Dec 11 10:59:04 CST 2017`
```

### hdfs dfsadmin -safemode 安全模式
NameNode在启动的时候 首先进入安全模式，如果datanode丢失的block达到一定的比例（由hdfs-site.xml文件中dfs.safemode.threshold.pct决定，默认0.999f），则系统会一直处于安全模式状态即只读状态 ； 否则没有其他情况影响，一般情况下，系统会自动离开安全模式。

dfs.safemode.threshold.pct 表示HDFS启动的时候，如果DataNode上报的 block个数0.999倍才可以离开安全模式，否则一直是这种只读状态。如果设为1则hdfs永远是处于SafeMode。

通常两种情况可以离开这处安全模式：

1、修改 dfs.safemode.threshold.pct为一个比较小的值，缺省值是0.999
2、hadoop dfsadmin -safemode leave 命令强制离开


用户可以使用命令行（hdfs dfsadmin -safemode value）做如下的操作：
```
# hdfs dfsadmin -safemode get    ## 返回安全模式是否开启的信息，返回 Safe mode is OFF/OPEN
# hdfs dfsadmin -safemode enter  ## 进入安全模工
# hdfs dfsadmin -safemode leave  ## 强制 NameNode 离开安全模式
# hdfs dfsadmin -safemode wait   ## 等待，一直到安全模式结束
```