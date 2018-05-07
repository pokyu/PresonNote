
## SparkSQL内置函数
### 字符串函数:
#### instr
- 返回值：int
- 说明：查找字符串str中子字符串substr出现的位置，如果查找失败将返回0，如果任一参数为Null将返回null，注意位置为从1开始的
- 示例：
```
spark-sql> select instr('tongboyutongboyutong','boyu');
5
Time taken: 0.057 seconds, Fetched 1 row(s)
spark-sql> select instr('tongboyutongboyutong','tong');
1
Time taken: 0.064 seconds, Fetched 1 row(s)
spark-sql>  select instr('tongboyutongboyutong','bo');
5
Time taken: 0.05 seconds, Fetched 1 row(s)
```
