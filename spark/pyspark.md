## pyspark
### 环境变更设置
使用pyspark的前提是设置了**SPARK_HOME**与**PYTHONPATH**两个参数，否则报如下错误：
```
"No module named pyspark" 
```
设置方法如下，例如我的客户端安装位置是/opt/hadoopclient：
```
export SPARK_HOME=/opt/hadoopclient/Spark/spark
export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.8.2.1-src.zip:$PYTHONPATH
```
测试程序如下：
```
from pyspark import SparkConf, SparkContext 
from pyspark.sql import HiveContext 
 
conf = (SparkConf() 
         .setAppName("My app") 
         .set("spark.executor.memory", "1g")) 
sc = SparkContext(conf = conf) 
sqlContext = HiveContext(sc) 
my_dataframe = sqlContext.sql("select count(*)  from tungdb.joina") 
my_dataframe.show() 
```