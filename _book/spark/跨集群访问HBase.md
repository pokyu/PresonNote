
### 5.1 安全集群模式下，Spark跨集群连接HBase

**背景：**
本端集群的Spark组件需要操作对端集群的HBase组件。
Spark程序运行模式为yarn-cluster模式。

**前置条件：**
两个集群已经互信。集群互信的基本条件是，本端集群和对端集群的版本必须完全一致，并且都为安全模式。

跨集群连接HBase的步骤如下：
1. **修改spark客户端的conf目录下的spark-defaults.conf，确保**
```
spark.hbase.obtainToken.enabled = true
spark.inputFormat.cache.enabled = false
```
这是为了开启Spark on HBase特性，安全模式下必须开启此特性，Spark中才能认证HBase，否则会报出GSSException：
```
javax.security.sasl.SaslException: GSS initiate failed [Caused by GSSException: No valid credentials provided (Mechanism level: Failed to find any Kerberos tgt)] 
```
通过实际投产情况，发现跨集群连接HBase的情况下，必须是通过修改spark-defaults.conf文件来开启Spark on HBase才有效果；如果不修改这个文件，用别的手段去修改参数（比如程序代码中直接指定配置项的值），虽然Spark的Environment信息中显示为开启，但是实际还是无法连接，仍然报出GSSException。

2. **继续修改spark-defaults.conf文件，将spark.yarn.cluster.driver.extraClassPath参数值，加入$PWD作为classpath的一部分，并保证$PWD在首位。**
例如，修改前为：
```
spark.yarn.cluster.driver.extraClassPath = /opt/huawei/Bigdata/FusionInsight/spark/cfg/:/opt/huawei/Bigdata/FusionInsight/spark/spark/lib/*
```
修改后为：
```
spark.yarn.cluster.driver.extraClassPath = $PWD:/opt/huawei/Bigdata/FusionInsight/spark/cfg/:/opt/huawei/Bigdata/FusionInsight/spark/spark/lib/*
```
如果不加入这一步，跨集群情况下，当前FusionInsight版本会存在无法刷新HBase Token导致token过期的问题。因为yarn内部启动container的shell脚本中，定义的classpath的顺序会导致刷新token时，优先读取节点上的配置文件，而不是Spark客户端提交的，导致刷新HBase Token时连接的zookeeper服务不是HBase所在集群的，而变成本集群的了，最终导致无法刷新HBase Token而连接过期。

3. **将spark客户端的conf目录下的hbase-site.xml，替换为对端集群的hbase-site.xml文件。**
4. ：**在spark-submit提交时，通过--files参数将对端集群hbase-site.xml文件发送到每个Executor**
```
$SPARK_HOME/bin/spark-submit \
--master yarn \
--deploy-mode cluster \
--driver-memory 2G \
--num-executors 5 \
--principal logc \
--keytab /logcAPP/SparkApp/SparkStreamingApp/conf/logc.keytab \
--jars $SPARK_HOME/lib/streamingClient/kafka-clients-0.8.2.1.jar,$SPARK_HOME/lib/streamingClient/kafka_2.10-0.8.2.1.jar,$SPARK_HOME/lib/streamingClient/spark-streaming-kafka_2.10-1.5.1.jar,/logcAPP/SparkApp/SparkStreamingApp/lib/commons-dbcp2-2.1.1.jar,/logcAPP/SparkApp/SparkStreamingApp/lib/commons-pool2-2.4.2.jar,/logcAPP/SparkApp/SparkStreamingApp/lib/ojdbc7.jar,/logcAPP/SparkApp/SparkStreamingApp/lib/fastjson-1.2.31.jar \
--files /logcAPP/SparkApp/SparkStreamingApp/conf/dbconfig.properties,/logcAPP/SparkApp/SparkStreamingApp/conf/BehaviorTraceInfo.properties,/logcAPP/SparkApp/SparkStreamingApp/conf/hbase-site.xml \
--name BehaviorTraceInfo_New \
--class com.berchina.iec.spark.streaming.BehaviorTraceInfo_New \
/logcAPP/SparkApp/SparkStreamingApp/BehaviorTraceInfo.jar cluster 300
```
5. **程序代码中，在创建HBase连接时，将--files参数发送过来的对端hbase-site.xml文件作为连接配置，再创建连接：**
```
public class HBaseTableFactory {
	private static Configuration conf = null;
	private static Connection conn = null;
	
	private Table hTable;
	
	public Table getTable() {
		return hTable;
	}

	static {
		init();
	}
	
	private static void init() {
		conf = HBaseConfiguration.create();
		
		conf.addResource(new Path(System.getProperty("user.dir") + File.separator + "hbase-site.xml"));
		System.err.println("配置读取测试：" + conf.get("hbase.zookeeper.quorum"));
		
	    try {
			conn = ConnectionFactory.createConnection(conf);
		} catch (IOException e) {
			System.err.println("ConnectionFactory.createConnection错误：" + e.getMessage());
		}
	}
	
	public HBaseTableFactory(String tableName) {
		try {
			hTable = conn.getTable(TableName.valueOf(tableName));
		} catch (IOException e) {
			System.err.println("conn.getTable(" + tableName + ")错误：" + e.getMessage());
		}
	}
}
```
注意，yarn-cluster模式下，无需在代码中编写安全认证相关代码。但是如果是长时间运行的Spark Streaming应用，会存在认证过期问题，需要指定principal和keytab参数，使得yarn能去定期认证。关于这一点，在后面的小节中会详细介绍。
