---
title: HDFS安全模式
category: Research
---

## hdfs dfsadmin -safemode 安全模式
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