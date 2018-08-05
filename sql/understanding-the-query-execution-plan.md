# MySQL执行计划
@(D. MySQL)

[toc]
## 执行计划结果：
### id
标识位，可以看出执行顺序。
执行原则：序号不同，大的优先执行，序号相同，从上向下执行。
### select_type
查询类型。select_type包括以下几种类型：

 - simple：表示不需要union操作或者不包含子查询的简单select查询。有连接查询时，外层的查询为simple，且只有一个
 - primary：一个需要union操作或者含有子查询的select，位于最外层的单位查询的select_type即为primary。且只有一个
 - union：union连接的两个select查询，第一个查询是dervied派生表，除了第一个表外，第二个以后的表select_type都是union
 - dependent union：与union一样，出现在union 或union all语句中，但是这个查询要受到外部查询的影响
 - union result：包含union的结果集，在union和union all语句中,因为它不需要参与查询，所以id字段为null
 - subquery：除了from字句中包含的子查询外，其他地方出现的子查询都可能是subquery
 - dependent subquery：与dependent union类似，表示这个subquery的查询要受到外部表查询的影响
 - derived：from字句中出现的子查询，也叫做派生表，其他数据库中可能叫做内联视图或嵌套select

### table
显示的查询表名，如果查询使用了别名，那么这里显示的是别名，如果不涉及对数据表的操作，那么这显示为null，如果显示为尖括号括起来的<derived N>就表示这个是临时表，后边的N就是执行计划中的id，表示结果来自于这个查询产生。如果是尖括号括起来的<union M,N>，与<derived N>类似，也是一个临时表，表示这个结果来自于union查询的id为M,N的结果集。
### partitions – 分区
partitions这列是建立在你的表是分区表才行。
### type – 查询结果类型
表示按照某种类型来查询，例如按照索引类型查找，按照范围查找，主要是以下几种类型：

 - const：表示 表中最多有一个匹配行
 - eq_ref：对于每个来自于前面的表的记录，从该表中读取唯一一行
 - ref：对于每个来自于前面的表的记录，所有匹配的行从这张表中取出
 - ref_or_null：类似于ref，但是可以搜索包含null值的行
 - index_merge：出现在使用一张表中的多个索引时，mysql会讲这多个索引合并到一起
 - range：按指定的范围来检索，很常见
 - index：从索取树中查找
 - ALL：全表扫描

从上面的分类描述中我们可以看出来，查询效率是根据从上往下排列的。

### possible_keys
查询可能使用到的索引都会在这里列出来。
### key
查询真正使用到的索引，select_type为index_merge时，这里可能出现两个以上的索引，其他的select_type这里只会出现一个。
### key_len
用于处理查询的索引长度，如果是单列索引，那就整个索引长度算进去，如果是多列索引，那么查询不一定都能使用到所有的列，具体使用到了多少个列的索引，这里就会计算进去，没有使用到的列，这里不会计算进去。

留意下这个列的值，算一下你的多列索引总长度就知道有没有使用到所有的列了。要注意，mysql的ICP特性使用到的索引不会计入其中。另外，key_len只计算where条件用到的索引长度，而排序和分组就算用到了索引，也不会计算到key_len中。

**如果key字段值为null，则key_len字段值也为null,而且对于key_len越小越好，当然不能为null.**
### ref
如果是使用的常数等值查询，这里会显示const，如果是连接查询，被驱动表的执行计划这里会显示驱动表的关联字段，如果是条件使用了表达式或者函数，或者条件列发生了内部隐式转换，这里可能显示为func
### rows
这里是执行计划中估算的扫描行数，不是精确值
### filtered
使用explain extended时会出现这个列，5.7之后的版本默认就有这个字段，不需要使用explain extended了。这个字段表示存储引擎返回的数据在server层过滤后，剩下多少满足查询的记录数量的比例，注意是百分比，不是具体记录数。 
### Extra
这个列可以显示的信息非常多，有几十种，常用的有：

 - distinct：在select部分使用了distinc关键字
 - no tables used：不带from字句的查询或者From dual查询
 - 使用not in()形式子查询或not exists运算符的连接查询，这种叫做反连接。即，一般连接查询是先查询内表，再查询外表，反连接就是先查询外表，再查询内表。
 - using filesort：排序时无法使用到索引时，就会出现这个。常见于order by和group by语句中
 - using index：查询时不需要回表查询，直接通过索引就可以获取查询的数据。
 - using join buffer（block nested loop），using join buffer（batched key accss）：5.6.x之后的版本优化关联查询的BNL，BKA特性。主要是减少内表的循环数量以及比较顺序地扫描查询。
 - using sort_union，using_union，using intersect，using sort_intersection：
 - using intersect：表示使用and的各个索引的条件时，该信息表示是从处理结果获取交集
 - using union：表示使用or连接各个使用索引的条件时，该信息表示从处理结果获取并集
 - using sort_union和using sort_intersection：与前面两个对应的类似，只是他们是出现在用and和or查询信息量大时，先查询主键，然后进行排序合并后，才能读取记录并返回。
 - using temporary：表示使用了临时表存储中间结果。临时表可以是内存临时表和磁盘临时表，执行计划中看不出来，需要查看status变量，used_tmp_table，used_tmp_disk_table才能看出来。
 - using where：表示存储引擎返回的记录并不是所有的都满足查询条件，需要在server层进行过滤。查询条件中分为限制条件和检查条件，5.6之前，存储引擎只能根据限制条件扫描数据并返回，然后server层根据检查条件进行过滤再返回真正符合查询的数据。5.6.x之后支持ICP特性，可以把检查条件也下推到存储引擎层，不符合检查条件和限制条件的数据，直接不读取，这样就大大减少了存储引擎扫描的记录数量。extra列显示using index condition
 - firstmatch(tb_name)：5.6.x开始引入的优化子查询的新特性之一，常见于where字句含有in()类型的子查询。如果内表的数据量比较大，就可能出现这个
 - loosescan(m..n)：5.6.x之后引入的优化子查询的新特性之一，在in()类型的子查询中，子查询返回的可能有重复记录时，就可能出现这个

除了这些之外，还有很多查询数据字典库，执行计划过程中就发现不可能存在结果的一些提示信息。

## 驱动表
1. 在同一层，上面的那张表是驱动表
2. join时，两张表，小的那张是驱动表，所以有大的结果集在是驱动表，那么sql有问题
3. left join在join左边的是驱动表
4. right join在join右边的是驱动表


## 总结：
尽量让自己的SQL用上索引，避免让extra里面出现file sort(文件排序),using temporary(使用临时表)。


## 问题：
### 1.是不是所有的index都要比ALL(Full Table Scan)块
示例：
```
[dba@3306][pokyudb]> desc salaries;
+-----------+---------+------+-----+---------+-------+
| Field     | Type    | Null | Key | Default | Extra |
+-----------+---------+------+-----+---------+-------+
| emp_no    | int(11) | NO   | PRI | NULL    |       |
| salary    | int(11) | NO   |     | NULL    |       |
| from_date | date    | NO   | PRI | NULL    |       |
| to_date   | date    | NO   |     | NULL    |       |
+-----------+---------+------+-----+---------+-------+
4 rows in set (0.00 sec)

[dba@3306][pokyudb]> select count(*) from salaries;
+----------+
| count(*) |
+----------+
|  2844047 |
+----------+
1 row in set (4.43 sec)

[dba@3306][pokyudb]> select count(salary) from salaries;
+---------------+
| count(salary) |
+---------------+
|       2844047 |
+---------------+
1 row in set (0.62 sec)

[dba@3306][pokyudb]> explain select count(*) from salaries;
+------+-------------+----------+-------+---------------+--------+---------+------+---------+-------------+
| id   | select_type | table    | type  | possible_keys | key    | key_len | ref  | rows    | Extra       |
+------+-------------+----------+-------+---------------+--------+---------+------+---------+-------------+
|    1 | SIMPLE      | salaries | index | NULL          | emp_no | 4       | NULL | 2844588 | Using index |
+------+-------------+----------+-------+---------------+--------+---------+------+---------+-------------+
1 row in set (0.00 sec)

[dba@3306][pokyudb]> explain select count(salary) from salaries;
+------+-------------+----------+------+---------------+------+---------+------+---------+-------+
| id   | select_type | table    | type | possible_keys | key  | key_len | ref  | rows    | Extra |
+------+-------------+----------+------+---------------+------+---------+------+---------+-------+
|    1 | SIMPLE      | salaries | ALL  | NULL          | NULL | NULL    | NULL | 2844588 |       |
+------+-------------+----------+------+---------------+------+---------+------+---------+-------+
1 row in set (0.00 sec)

[dba@3306][pokyudb]> explain select count(emp_no) from salaries;
+------+-------------+----------+-------+---------------+--------+---------+------+---------+-------------+
| id   | select_type | table    | type  | possible_keys | key    | key_len | ref  | rows    | Extra       |
+------+-------------+----------+-------+---------------+--------+---------+------+---------+-------------+
|    1 | SIMPLE      | salaries | index | NULL          | emp_no | 4       | NULL | 2844588 | Using index |
+------+-------------+----------+-------+---------------+--------+---------+------+---------+-------------+
1 row in set (0.01 sec)

[dba@3306][pokyudb]> select count(emp_no) from salaries;
+---------------+
| count(emp_no) |
+---------------+
|       2844047 |
+---------------+
1 row in set (4.42 sec)
```
从上面的示例，可以看出，走index时，速度要明显慢于ALL。
但这是不正常的。
正常count(*)要比count(字段)要快，因为mysql对count(*)做了优化。
默认count(*)就是走主键，因为innodb是聚餐索引，检查数据直接通过聚餐索引检查数据的
第二天，执行计划恢复了。count(*)正常了。

可能原因：
这个表的数据是再查询之前几个小时内insert进去的。

在主上面执行这些查询期间，主上面可能有大量update语句更新了salary表，这样，使用emp_no索引做count(*)，需要用被更新的行的emp_no上的索引行找回到主表的对应行看是否可见，也就是有大量找回主表数据行的操作，无法直接简单地扫emp_no来数行。导致很慢。 当一段时间不更新salary表后，purge操作完成后，就可以直接扫emp_no索引完成count(*)了。

正常和不正常的从执行计划上看确实是相同的，问题是对salary表做更新、删除和插入导致的，被purge的时间，与innodb purge的进度有关，不一定的。 purge不能保证在多长时间内完成purge
