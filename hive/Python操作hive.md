## python连接hive

1. 安装sasl模块和thrift模块。python setup.py install
所有文件都在python-examples目录下

2.	如果连接到安全模式的集群，则在python-examples/pyCLI_sec.py中的hosts数组中填写安装HiveServer的节点的业务平面IP。
3.	
执行以下命令运行客户端程序：
kinit -k -t keytab的存储路径 username
cd python-examples 
python pyCLI_sec.py

```
from pyhs2.haconnection import HAConnection

#"xxx.xxx.xxx.xxx"is ip address
hosts = ["10.14.18.203", "10.14.18.204", "10.14.18.205"]
conf = {"krb_host":"hadoop.hadoop.com", "krb_service":"hive"}
try:
    with HAConnection(hosts = hosts,
                       port = 21066,
                       authMechanism = "KERBEROS",
                       configuration = conf) as haConn:
        with haConn.getConnection() as conn:
            with conn.cursor() as cur:
                # Show databases
                
                print cur.getDatabases()
                
                # Execute query
                cur.execute("show tables")
                
                # Return column info from query
                print cur.getSchema()
                
                # Fetch table results
                for i in cur.fetch():
                    print i
                    
except Exception, e:
    print e
```