---
title: Hive管理
category: Operations Guide
---


## Hive

### 在安装Hive Metastore的主机上查找当前FS根目录：

```shell
[root@cvm-da-datasvr-whd4 conf.server]# hive --config /etc/hive/conf/conf.server --service metatool -listFSRoot
Initializing HiveMetaTool..
2018-10-16 14:17:19,830 INFO  [main] metastore.ObjectStore: ObjectStore, initialize called
2018-10-16 14:17:19,949 INFO  [main] DataNucleus.Persistence: Property datanucleus.fixedDatastore unknown - will be ignored
2018-10-16 14:17:19,949 INFO  [main] DataNucleus.Persistence: Property hive.metastore.integral.jdo.pushdown unknown - will be ignored
2018-10-16 14:17:19,950 INFO  [main] DataNucleus.Persistence: Property datanucleus.cache.level2 unknown - will be ignored
Tue Oct 16 14:17:20 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Tue Oct 16 14:17:20 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Tue Oct 16 14:17:20 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Tue Oct 16 14:17:20 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
2018-10-16 14:17:20,639 INFO  [main] metastore.ObjectStore: Setting MetaStore object pin classes with hive.metastore.cache.pinobjtypes="Table,Database,Type,FieldSchema,Order"
Tue Oct 16 14:17:21 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Tue Oct 16 14:17:21 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Tue Oct 16 14:17:21 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Tue Oct 16 14:17:21 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
2018-10-16 14:17:21,785 INFO  [main] metastore.MetaStoreDirectSql: Using direct SQL, underlying DB is MYSQL
2018-10-16 14:17:21,786 INFO  [main] metastore.ObjectStore: Initialized ObjectStore
Listing FS Roots..
hdfs://wcluster/apps/hive/warehouse/test_db.db
hdfs://wcluster/apps/hive/warehouse/chris.db
hdfs://wcluster/apps/hive/warehouse
```

