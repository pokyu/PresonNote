## HDFS的目录配额

### 文件数配额
- 1.查看
**hadoop fs -count -q /folder**
```
[root@CNSZ431014 ~]# hadoop fs -count -q /pokyu
        none             inf            none             inf            1            2         6912842208 /pokyu
```
依次表示为：文件数限额  可用文件数  空间限额 可用空间 目录数  文件数  总大小 文件/目录名
- 2.清除
**hadoop dfsadmin -clrQuota  /folder**
```
[root@CNSZ431014 ~]# hadoop fs -count -q /pokyu
           3               0            none             inf            1            2         6912842208 /pokyu
[root@CNSZ431014 ~]# hadoop dfsadmin -clrQuota /pokyu
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.
[root@CNSZ431014 ~]# hadoop dfs -count -q /pokyu     
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

        none             inf      1073741824      1073741344            1           10                160 /pokyu
```

- 3.设置
**hadoop dfsadmin -setQuota n /folder**
```
[root@CNSZ431014 ~]# hadoop fs -count -q /pokyu
        none             inf            none             inf            1            2         6912842208 /pokyu
[root@CNSZ431014 ~]# hadoop dfsadmin -setQuota 3 /pokyu
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.
[root@CNSZ431014 ~]# hadoop fs -count -q /pokyu
           3               0            none             inf            1            2         6912842208 /pokyu
```
- 4.报错
```ls 
[root@CNSZ431014 ~]# hadoop dfs -put ./ztest.txt /pokyu
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.
put: The NameSpace quota (directories and files) of directory /pokyu is exceeded: quota=3 file count=4
```
### 空间配额
#### 设置
**hadoop dfsadmin -setSpaceQuota 128m /folder**

#### 查看
**hadoop fs -count -q /folder**
```
[root@CNSZ431014 ~]# hadoop dfs -count -q /pokyu               
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

           3               1       134217728    -10235045584            1            1         3456421104 /pokyu
```
查看空间配额，注意这里的空间配额是把副本容量也算入的，也就是所我们这里的配额控制的是file_size x replications
#### 清除
**hadoop dfsadmin -clrSpaceQuota /folder**
```
[root@CNSZ431014 ~]# hadoop dfs -count -q /pokyu     
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

        none             inf      1073741824      1073741344            1           10                160 /pokyu
[root@CNSZ431014 ~]# hadoop dfsadmin -clrSpaceQuota /pokyu
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

[root@CNSZ431014 ~]# hadoop dfs -count -q /pokyu          
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

        none             inf            none             inf            1           10                160 /pokyu
```
#### 报错
```
[root@CNSZ431014 ~]# hadoop dfs -count -q /pokyu               
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

           3               2       136314880       136314880            1            0                  0 /pokyu
[root@CNSZ431014 ~]# hadoop dfs -put ztest.txt /pokyu          
DEPRECATED: Use of this script to execute hdfs command is deprecated.
Instead use the hdfs command for it.

put: The DiskSpace quota of /pokyu is exceeded: quota = 136314880 B = 130 MB but diskspace consumed = 402653184 B = 384 MB
```
ztest.txt仅4k大小，配额管理是以块为单位的，即使我们的数据不到1M，他也会按一个block来计算。而且是算上副本数的，即128M*3=384 MB