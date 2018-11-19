---
title: 安装HDP集群
category: Operations Guide
---
# Install Hadoop Cluster for HDP

HADOOP离线集群安装 （HDP）

## 环境

**主机信息：**
```
10.60.16.168 cvm-da-datasvr-whd1.hadoop.com cvm-da-datasvr-whd1
10.60.16.201 cvm-da-datasvr-whd2.hadoop.com cvm-da-datasvr-whd2
10.60.16.62  cvm-da-datasvr-whd3.hadoop.com cvm-da-datasvr-whd3
10.60.16.39  cvm-da-datasvr-whd4.hadoop.com cvm-da-datasvr-whd4
10.60.16.214 cvm-da-datasvr-whd5.hadoop.com cvm-da-datasvr-whd5
10.60.16.150 cvm-da-datasvr-whd6.hadoop.com cvm-da-datasvr-whd6
10.60.16.213 cvm-da-datasvr-whd7.hadoop.com cvm-da-datasvr-whd7
```

**账号密码：**

| 序号 | 类型 | 账号 | 密码 | 用途 |
| --- | --- | --- | --- | --- |
| 1   | 主机 | root | 通用 |
| 2   | DB | root | test1234 |
| 3   | DB | ambari | test1234 |
| 4   | KMS Master Secret | N/A | test1234 | KMS Master Secret |
| 5   | DB | ranger | test1234 |
| 6   | DB | rangerkms | test1234 |
| 7   | DB | hive | test1234 |
| 8   | Ambari | admin | test1234 |  Ambari UI |
| 9   | Ambari | N/A | Muli1234 |Password for TrustStore |
| 10  | Grafana Admin Password | admin | test1234 | Grafana Admin Password |
| 11  | Ranger | admin | test1234 |   |
| 12  | AD | hdfs | test1234 | CN=hdfs,OU=Service_Account,DC=wesure,DC=cn |
| 13  | AD | hive | test1234 | CN=hive,OU=Service_Account,DC=wesure,DC=cn |
| 14  | AD | yarn | test1234 | CN=yarn,OU=Service_Account,DC=wesure,DC=cn |
| 15  | AD | druid | test1234 | CN=druid,OU=Service_Account,DC=wesure,DC=cn |
| 16  | AD | hadoop_ldap | test1234 | CN=hadoop_ldap,OU=Service_Account,DC=wesure,DC=cn |


```
AD服务器地址：
Address: 10.95.10.122
Address: 10.95.10.239

hadoop_ldap可操作的目录：OU=servers_hadoop,DC=wesure,DC=cn

```

需要AD管理员提供证书文件：
步骤待补充

测试环境密码：ws1test2
AD信息：
```
10.0.16.14
WESURETEST.CN
ldaps://10.0.16.14:636
OU=servers_hadoop,DC=wesuretest,DC=cn
wesuretest.cn,.wesuretest.cn
hadoop_ldap@WESURETEST.CN
H@doop!@#

ambari_logsearch_admin admin
```



## 集群安装前准备

### SSH免密登录
每台主机执行如下命令：
```shell
ssh-keygen -t rsa

ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.60.16.168
ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.60.16.201
ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.60.16.62
ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.60.16.39
ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.60.16.214
ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.60.16.150
ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.60.16.213
```

### 修改hosts
对所有主机增加ip与主机名映射关系
```shell
[root@cvm-da-datasvr-whd1 .ssh]# cat /etc/hosts
127.0.0.1  localhost  localhost.localdomain #cvm-da-datasvr-whd1
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

10.60.16.168 cvm-da-datasvr-whd1.hadoop.com cvm-da-datasvr-whd1
10.60.16.201 cvm-da-datasvr-whd2.hadoop.com cvm-da-datasvr-whd2
10.60.16.62  cvm-da-datasvr-whd3.hadoop.com cvm-da-datasvr-whd3
10.60.16.39  cvm-da-datasvr-whd4.hadoop.com cvm-da-datasvr-whd4
10.60.16.214 cvm-da-datasvr-whd5.hadoop.com cvm-da-datasvr-whd5
10.60.16.150 cvm-da-datasvr-whd6.hadoop.com cvm-da-datasvr-whd6
10.60.16.213 cvm-da-datasvr-whd7.hadoop.com cvm-da-datasvr-whd7
```
### MySql安装与配置
#### MySql安装
创建mysql用户：
```
groupadd mysql
useradd -g mysql mysql
```

创建目录：
```
mkdir -p /var/lib/mysql/
mkdir -p /var/log/mysql/
mkdir -p /data/mysql/3306
mkdir -p /opt/cdh/mysql

chown -R mysql:mysql /var/lib/mysql
chown -R mysql:mysql /var/log/mysql/
chown -R mysql:mysql /data/mysql/
chown -R mysql:mysql /opt/cdh/mysql
```
将mysql-5.7.18-linux-glibc2.5-x86_64.tar.gz上传到主机上。
解压：
```
mkdir /usr/local/mysql
cd /usr/local/mysql
chown -R mysql:mysql /usr/local/mysql
su - mysql
tar zxvf mysql-5.7.18-linux-glibc2.5-x86_64.tar.gz 
mv mysql-5.7.18-linux-glibc2.5-x86_64 mysql-5.7.18
```

配置/etc/my.cnf

```shell
[client]
socket = /var/lib/mysql/mysql3306.sock
port = 3306
[mysqld]
character-set-server = utf8
default-storage-engine = InnoDB
port = 3306
socket = /var/lib/mysql/mysql3306.sock
basedir = /usr/local/mysql/mysql-5.7.18
datadir = /data/mysql/3306
skip-external-locking
key_buffer_size = 384M
max_allowed_packet = 100M
table_open_cache = 2000
sort_buffer_size = 2M
read_buffer_size = 2M
read_rnd_buffer_size = 8M
myisam_sort_buffer_size = 64M
thread_cache_size = 8
max_connections = 4096
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
query_cache_type =0
query_cache_size = 0
local-infile=0
log-bin=mysql-bin
expire_logs_days=30
server-id = 2
binlog_format=ROW
log-error= /var/log/mysql/mysql3306.log
innodb_data_home_dir = /data/mysql/3306
innodb_data_file_path = ibdata1:1024M:autoextend
innodb_log_group_home_dir = /data/mysql/3306
innodb_buffer_pool_size = 536870912
innodb_log_file_size = 100M
innodb_log_buffer_size = 8M
innodb_flush_log_at_trx_commit = 1
innodb_lock_wait_timeout = 50
sync_binlog = 1
log_bin_trust_function_creators=1
[mysqldump]
quick
max_allowed_packet = 100M
[mysql]
no-auto-rehash
[myisamchk]
key_buffer_size = 256M
sort_buffer_size = 256M
read_buffer = 2M
write_buffer = 2M
[mysqlhotcopy]
interactive-timeout
```

初始化mysql
```
su - root
cd /usr/local/mysql/mysql-5.7.18/bin
./mysqld --defaults-file=/etc/my.cnf --initialize
```
验证
tail -f /var/log/mysql/mysql3306.log

```
[root@cvm-da-datasvr-whd1 ~]# tail -f /var/log/mysql/mysql3306.log
2018-10-08T09:29:31.259606Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
 100 200 300 400 500 600 700 800 900 1000
 100
 100
2018-10-08T09:29:44.196533Z 0 [Warning] InnoDB: New log files created, LSN=45791
2018-10-08T09:29:44.370420Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-10-08T09:29:44.455597Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: b0ea3837-cadc-11e8-b8bb-525400763261.
2018-10-08T09:29:44.459604Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-10-08T09:29:44.463800Z 1 [Note] A temporary password is generated for root@localhost: 8q;#yua_o_bG
```
查看到默认生成的密码 root@localhost: 8q;#yua_o_bG

修改权限：
由于是由root初始化的，所以需要更改一下文件的用户属主
```
log-error= /var/log/mysql/mysql3306.log
chown -R mysql:mysql /var/log/mysql/mysql3306.log
chown -R mysql:mysql /var/lib/mysql
chown -R mysql:mysql /var/log/mysql/
chown -R mysql:mysql /data/mysql/
```

启动mysql
```
su - mysql
cd /usr/local/mysql/mysql-5.7.18/bin
./mysqld_safe &
```

验证并修改root密码：
```
./mysql -uroot -p --socket=/var/lib/mysql/mysql3306.sock

SET PASSWORD FOR 'root'@'localhost' = PASSWORD('test1234');
grant all privileges on *.* to root@'10.60.%' identified by 'test1234';
grant all privileges on *.* to root@'10.60.%' WITH GRANT OPTION;
flush privileges;
```


### 安装mysql客户端
```
yum install mysql
```

## 安装Ambari


### 创建本地ambari数据源
安装httpd服务：
```
yum -y install httpd
systemctl  restart  httpd
systemctl  status  httpd -l
```

解压ambari-2.6.2.2-centos7.tar.gz
```
mkdir /data/repo_source
cd /var/www/html
tar zxvf ambari-2.6.2.2-centos7.tar.gz
```

创建数据源连接
```
cd /var/www/html
ln -s /data/repo_source/ambari ambari
```

同样方法添加hdp源
```
cd /data/repo_source/hdp
tar zxvf HDP-2.6.5.0-centos7-rpm.tar.gz
tar zxvf HDP-GPL-2.6.5.0-centos7-gpl.tar.gz
tar zxvf HDP-UTILS-1.1.0.22-centos7.tar.gz

cd /var/www/html
ln -s /data/repo_source/hdp hdp
```

添加ambari源文件

```
[root@cvm-da-datasvr-whd1 ambari]# ll /etc/yum.repos.d
total 8
-rw-r--r-- 1 root root 1403 Oct  8 15:22 CentOS-Base.repo
-rw-r--r-- 1 root root  220 Oct  8 15:22 CentOS-Epel.repo
[root@cvm-da-datasvr-whd1 ambari]# ll
total 1782788
-rw-r--r-- 1 root root 1823779835 Oct  8 18:01 ambari-2.6.2.2-centos7.tar.gz
-rw-r--r-- 1 root root        437 Sep 26 20:41 ambari.repo
drwxr-xr-x 3 root root       4096 Oct  8 18:01 centos7
[root@cvm-da-datasvr-whd1 ambari]# cp ambari.repo /etc/yum.repos.d/
[root@cvm-da-datasvr-whd1 ambari]# ll /etc/yum.repos.d
total 12
-rw-r--r-- 1 root root 1403 Oct  8 15:22 CentOS-Base.repo
-rw-r--r-- 1 root root  220 Oct  8 15:22 CentOS-Epel.repo
-rw-r--r-- 1 root root  437 Oct  8 19:35 ambari.repo
[root@cvm-da-datasvr-whd1 ambari]#
```
或
```
wget -nv http://public-repo-1.hortonworks.com/ambari/centos7/2.x/updates/2.7.0.0/ambari.repo -O /etc/yum.repos.d/ambari.repo
```

ambari.repo配置更改如下
```
[root@cvm-da-datasvr-whd1 yum.repos.d]# cat ambari.repo
#VERSION_NUMBER=2.6.2.2-1
[ambari-2.6.2.2]
name=ambari Version - ambari-2.6.2.2
#baseurl=http://public-repo-1.hortonworks.com/ambari/centos7/2.x/updates/2.6.2.2
baseurl=http://10.60.16.168/ambari/centos7/2.6.2.2-1
gpgcheck=1
#gpgkey=http://public-repo-1.hortonworks.com/ambari/centos7/2.x/updates/2.6.2.2/RPM-GPG-KEY/RPM-GPG-KEY-Jenkins
gpgkey=http://10.60.16.168/ambari/centos7/2.6.2.2-1/RPM-GPG-KEY/RPM-GPG-KEY-Jenkins
enabled=1
priority=1
```

验证数据源是否OK
yum repolist命令验证，正确如下

```
[root@cvm-da-datasvr-whd1 yum.repos.d]# yum repolist
Failed to set locale, defaulting to C
Loaded plugins: fastestmirror, langpacks
http://10.60.16.168/ambari/centos7/2.6.2.2-1/repodata/repomd.xml: [Errno 14] HTTP Error 403 - Forbidden
Trying other mirror.
To address this issue please refer to the below knowledge base article

https://access.redhat.com/solutions/69319

If above article doesn't help to resolve this issue please create a bug on https://bugs.centos.org/

http://10.60.16.168/ambari/centos7/2.6.2.2-1/repodata/repomd.xml: [Errno 14] HTTP Error 403 - Forbidden
Trying other mirror.
repo id                                                    repo name                                                                   status
ambari-2.6.2.2                                             ambari Version - ambari-2.6.2.2                                                 0
epel/7/x86_64                                              EPEL for redhat/centos 7 - x86_64                                           12721
extras/7/x86_64                                            Qcloud centos extras - x86_64                                                 432
os/7/x86_64                                                Qcloud centos os - x86_64                                                    9911
updates/7/x86_64                                           Qcloud centos updates - x86_64                                               1540
repolist: 24604
[root@cvm-da-datasvr-whd1 yum.repos.d]#
```
验证源是否可以正常访问
```
[root@cvm-da-datasvr-whd1 ambari]# wget http://10.60.16.168/ambari/centos7/2.6.2.2-1/
--2018-10-08 20:33:55--  http://10.60.16.168/ambari/centos7/2.6.2.2-1/
Connecting to 10.60.16.168:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2645 (2.6K) [text/html]
Saving to: 'index.html'

100%[===================================================================================================>] 2,645       --.-K/s   in 0s

2018-10-08 20:33:55 (277 MB/s) - 'index.html' saved [2645/2645]

[root@cvm-da-datasvr-whd1 ambari]#
```


### 安装ambari-server
```
[root@cvm-da-datasvr-whd1 ~]# which ambari-server
/usr/bin/which: no ambari-server in (/usr/local/java//bin:/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin)
[root@cvm-da-datasvr-whd1 ~]# yum install ambari-server
Failed to set locale, defaulting to C
Loaded plugins: fastestmirror, langpacks
ambari-2.6.2.2                                                                                                        | 2.9 kB  00:00:00
ambari-2.6.2.2/primary_db                                                                                             | 8.7 kB  00:00:00
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package ambari-server.x86_64 0:2.6.2.2-1 will be installed
--> Processing Dependency: postgresql-server >= 8.1 for package: ambari-server-2.6.2.2-1.x86_64
--> Running transaction check
---> Package postgresql-server.x86_64 0:9.2.24-1.el7_5 will be installed
--> Processing Dependency: postgresql-libs(x86-64) = 9.2.24-1.el7_5 for package: postgresql-server-9.2.24-1.el7_5.x86_64
--> Processing Dependency: postgresql(x86-64) = 9.2.24-1.el7_5 for package: postgresql-server-9.2.24-1.el7_5.x86_64
--> Processing Dependency: libpq.so.5()(64bit) for package: postgresql-server-9.2.24-1.el7_5.x86_64
--> Running transaction check
---> Package postgresql.x86_64 0:9.2.24-1.el7_5 will be installed
---> Package postgresql-libs.x86_64 0:9.2.24-1.el7_5 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=============================================================================================================================================
 Package                              Arch                      Version                              Repository                         Size
=============================================================================================================================================
Installing:
 ambari-server                        x86_64                    2.6.2.2-1                            ambari-2.6.2.2                    718 M
Installing for dependencies:
 postgresql                           x86_64                    9.2.24-1.el7_5                       updates                           3.0 M
 postgresql-libs                      x86_64                    9.2.24-1.el7_5                       updates                           234 k
 postgresql-server                    x86_64                    9.2.24-1.el7_5                       updates                           3.8 M

Transaction Summary
=============================================================================================================================================
Install  1 Package (+3 Dependent packages)

Total download size: 725 M
Installed size: 815 M
Is this ok [y/d/N]: y
Downloading packages:
(1/4): postgresql-libs-9.2.24-1.el7_5.x86_64.rpm                                                                      | 234 kB  00:00:00
(2/4): postgresql-9.2.24-1.el7_5.x86_64.rpm                                                                           | 3.0 MB  00:00:00
(3/4): postgresql-server-9.2.24-1.el7_5.x86_64.rpm                                                                    | 3.8 MB  00:00:00
warning: /var/cache/yum/x86_64/7/ambari-2.6.2.2/packages/ambari-server-2.6.2.2-1.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID 07513cad: NOKEY
Public key for ambari-server-2.6.2.2-1.x86_64.rpm is not installed
(4/4): ambari-server-2.6.2.2-1.x86_64.rpm                                                                             | 718 MB  00:00:12
---------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                         55 MB/s | 725 MB  00:00:13
Retrieving key from http://10.60.16.168/ambari/centos7/2.6.2.2-1/RPM-GPG-KEY/RPM-GPG-KEY-Jenkins
Importing GPG key 0x07513CAD:
 Userid     : "Jenkins (HDP Builds) <jenkin@hortonworks.com>"
 Fingerprint: df52 ed4f 7a3a 5882 c099 4c66 b973 3a7a 0751 3cad
 From       : http://10.60.16.168/ambari/centos7/2.6.2.2-1/RPM-GPG-KEY/RPM-GPG-KEY-Jenkins
Is this ok [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : postgresql-libs-9.2.24-1.el7_5.x86_64                                                                                     1/4
  Installing : postgresql-9.2.24-1.el7_5.x86_64                                                                                          2/4
  Installing : postgresql-server-9.2.24-1.el7_5.x86_64                                                                                   3/4
  Installing : ambari-server-2.6.2.2-1.x86_64                                                                                            4/4
  Verifying  : postgresql-server-9.2.24-1.el7_5.x86_64                                                                                   1/4
  Verifying  : postgresql-libs-9.2.24-1.el7_5.x86_64                                                                                     2/4
  Verifying  : postgresql-9.2.24-1.el7_5.x86_64                                                                                          3/4
  Verifying  : ambari-server-2.6.2.2-1.x86_64                                                                                            4/4

Installed:
  ambari-server.x86_64 0:2.6.2.2-1

Dependency Installed:
  postgresql.x86_64 0:9.2.24-1.el7_5        postgresql-libs.x86_64 0:9.2.24-1.el7_5        postgresql-server.x86_64 0:9.2.24-1.el7_5

Complete!

[root@cvm-da-datasvr-whd1 ~]# which ambari-server
/usr/sbin/ambari-server
[root@cvm-da-datasvr-whd1 ~]#
```


### 初始化元数据库
**账号密码：**
```
mysql -u root -p
CREATE USER 'ambari'@'localhost' IDENTIFIED BY 'test1234';
GRANT ALL PRIVILEGES ON *.* TO 'ambari'@'localhost';
CREATE USER 'ambari'@'10.60.%' IDENTIFIED BY 'test1234';
GRANT ALL PRIVILEGES ON *.* TO 'ambari'@'10.60.%';
FLUSH PRIVILEGES;
```
**创建数据库：**
加载Ambari Server数据库脚本(脚本在Ambari Server主机的 /var/lib/ambari-server/resources/Ambari-DDL-MySQL-CREATE.sql)
```
mysql -u ambari -p
MySQL [(none)]> CREATE DATABASE ambari;
MySQL [(none)]> USE ambari;
MySQL [ambari]> SOURCE /var/lib/ambari-server/resources/Ambari-DDL-MySQL-CREATE.sql;
```

建立mysql与ambari-server的连接
上传mysql驱动mysql-connector-java-5.1.47.jar到/var/lib/ambari-server/resources目录下
```
[root@cvm-da-datasvr-whd1 resources]# ll /usr/share/java
total 0
[root@cvm-da-datasvr-whd1 resources]# cp mysql-connector-java-5.1.47.jar /usr/share/java/mysql-connector-java.jar
[root@cvm-da-datasvr-whd1 resources]# ll /usr/share/java
total 988
-rw-r--r-- 1 root root 1007502 Oct  8 21:01 mysql-connector-java.jar
```


### 初始化设置ambari-server并启动

```
vim /etc/ambari-server/conf/ambari.properties


jdk1.8.url=http://public-repo-1.hortonworks.com/ARTIFACTS/jdk-8u112-linux-x64.tar.gz
改成
jdk1.8.url=http://10.60.16.168/ambari/ARTIFACTS/jdk-8u112-linux-x64.tar.gz

jdk1.8.jcpol-url=http://public-repo-1.hortonworks.com/ARTIFACTS/jce_policy-8.zip
改成
jdk1.8.jcpol-url=http://10.60.16.168/ambari/ARTIFACTS/jce_policy-8.zip
```

```
mkdir /data/repo_source/ambari/ARTIFACTS
cd /data/repo_source/ambari/ARTIFACTS
上传
jdk-8u112-linux-x64.tar.gz与jce_policy-8.zip
```


```
ambari-server setup --jdbc-db=mysql --jdbc-driver=/usr/share/java/mysql-connector-java.jar

[root@cvm-da-datasvr-whd1 resources]# ambari-server setup --jdbc-db=mysql --jdbc-driver=/usr/share/java/mysql-connector-java.jar
Using python  /usr/bin/python
Setup ambari-server
Copying /usr/share/java/mysql-connector-java.jar to /var/lib/ambari-server/resources
If you are updating existing jdbc driver jar for mysql with mysql-connector-java.jar. Please remove the old driver jar, from all hosts. Restarting services that need the driver, will automatically copy the new jar to the hosts.
JDBC driver was successfully initialized.
Ambari Server 'setup' completed successfully.
[root@cvm-da-datasvr-whd1 resources]#
```


```
[root@cvm-da-datasvr-whd1 ~]# ambari-server setup
Using python  /usr/bin/python
Setup ambari-server
Checking SELinux...
SELinux status is 'disabled'
Customize user account for ambari-server daemon [y/n] (n)? n
Adjusting ambari-server permissions and ownership...
Checking firewall status...
Checking JDK...
[1] Oracle JDK 1.8 + Java Cryptography Extension (JCE) Policy Files 8
[2] Oracle JDK 1.7 + Java Cryptography Extension (JCE) Policy Files 7
[3] Custom JDK
==============================================================================
Enter choice (1): 1
To download the Oracle JDK and the Java Cryptography Extension (JCE) Policy Files you must accept the license terms found at http://www.oracle.com/technetwork/java/javase/terms/license/index.html and not accepting will cancel the Ambari Server setup and you must install the JDK and JCE files manually.
Do you accept the Oracle Binary Code License Agreement [y/n] (y)? y
Downloading JDK from http://10.60.16.168/ambari/ARTIFACTS/jdk-8u112-linux-x64.tar.gz to /var/lib/ambari-server/resources/jdk-8u112-linux-x64.tar.gz
jdk-8u112-linux-x64.tar.gz... 100% (174.7 MB of 174.7 MB)
Successfully downloaded JDK distribution to /var/lib/ambari-server/resources/jdk-8u112-linux-x64.tar.gz
Installing JDK to /usr/jdk64/
Successfully installed JDK to /usr/jdk64/
Downloading JCE Policy archive from http://10.60.16.168/ambari/ARTIFACTS/jce_policy-8.zip to /var/lib/ambari-server/resources/jce_policy-8.zip

Successfully downloaded JCE Policy archive to /var/lib/ambari-server/resources/jce_policy-8.zip
Installing JCE policy...
Checking GPL software agreement...
GPL License for LZO: https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html
Enable Ambari Server to download and install GPL Licensed LZO packages [y/n] (n)? y
Completing setup...
Configuring database...
Enter advanced database configuration [y/n] (n)? y
Configuring database...
==============================================================================
Choose one of the following options:
[1] - PostgreSQL (Embedded)
[2] - Oracle
[3] - MySQL / MariaDB
[4] - PostgreSQL
[5] - Microsoft SQL Server (Tech Preview)
[6] - SQL Anywhere
[7] - BDB
==============================================================================
Enter choice (1): 3
Hostname (localhost): 10.60.16.168
Port (3306): 3306
Database name (ambari): ambari
Username (ambari): ambari
Enter Database Password (bigdata):
Re-enter password:
Configuring ambari database...
Configuring remote database connection properties...
WARNING: Before starting Ambari Server, you must run the following DDL against the database to create the schema: /var/lib/ambari-server/resources/Ambari-DDL-MySQL-CREATE.sql
Proceed with configuring remote database connection properties [y/n] (y)? y
Extracting system views...
......ambari-admin-2.6.2.2.1.jar
.....
Adjusting ambari-server permissions and ownership...
Ambari Server 'setup' completed successfully.
[root@cvm-da-datasvr-whd1 ~]#
```


## 安装组件

### 准备
```

HDP-2.6
http://10.60.16.168/hdp/HDP/centos7/2.6.5.0-292/
HDP-UTILS-1.1.0.22
http://10.60.16.168/hdp/HDP-UTILS/centos7/1.1.0.22/

cvm-da-datasvr-whd2.hadoop.com
cvm-da-datasvr-whd3.hadoop.com
cvm-da-datasvr-whd4.hadoop.com
cvm-da-datasvr-whd5.hadoop.com
cvm-da-datasvr-whd6.hadoop.com
cvm-da-datasvr-whd7.hadoop.com
```

```
CREATE USER 'hive'@'localhost' IDENTIFIED BY 'test1234';
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'localhost';
CREATE USER 'hive'@'10.60.%' IDENTIFIED BY 'test1234';
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'10.60.%';
FLUSH PRIVILEGES;
CREATE DATABASE hive;

```

```
CREATE USER 'ranger'@'localhost' IDENTIFIED BY 'test1234';
GRANT ALL PRIVILEGES ON *.* TO 'ranger'@'localhost';
CREATE USER 'ranger'@'10.60.%' IDENTIFIED BY 'test1234';
GRANT ALL PRIVILEGES ON *.* TO 'ranger'@'10.60.%';
FLUSH PRIVILEGES;
CREATE DATABASE ranger;

CREATE USER 'rangerkms'@'localhost' IDENTIFIED BY 'test1234';
GRANT ALL PRIVILEGES ON *.* TO 'rangerkms'@'localhost';
CREATE USER 'rangerkms'@'10.60.%' IDENTIFIED BY 'test1234';
GRANT ALL PRIVILEGES ON *.* TO 'rangerkms'@'10.60.%';
FLUSH PRIVILEGES;
CREATE DATABASE rangerkms;
```





## 开启kerberos准备工作

从kerberos服务器上下载CA证书。
开始
```shell
[root@cvm-da-datasvr-whd1 temp]# ambari-server setup-security
Using python  /usr/bin/python
Security setup options...
===========================================================================
Choose one of the following options:
  [1] Enable HTTPS for Ambari server.
  [2] Encrypt passwords stored in ambari.properties file.
  [3] Setup Ambari kerberos JAAS configuration.
  [4] Setup truststore.
  [5] Import certificate to truststore.
===========================================================================
Enter choice, (1-5): 2
Please provide master key for locking the credential store:
Re-enter master key:
Do you want to persist master key. If you choose not to persist, you need to provide the Master Key while starting the ambari server as an env variable named AMBARI_SECURITY_MASTER_KEY or the start will prompt for the master key. Persist [y/n] (y)?
Adjusting ambari-server permissions and ownership...
Ambari Server 'setup-security' completed successfully.
```

```shell
[root@cvm-da-datasvr-whd1 temp]# ambari-server setup-security
Using python  /usr/bin/python
Security setup options...
===========================================================================
Choose one of the following options:
  [1] Enable HTTPS for Ambari server.
  [2] Encrypt passwords stored in ambari.properties file.
  [3] Setup Ambari kerberos JAAS configuration.
  [4] Setup truststore.
  [5] Import certificate to truststore.
===========================================================================
Enter choice, (1-5): 4
Do you want to configure a truststore [y/n] (y)? y
TrustStore type [jks/jceks/pkcs12] (jks):jks
Path to TrustStore file :/etc/ambari-server/cert/hadoop.cer
Password for TrustStore:
Re-enter password:
Ambari Server 'setup-security' completed successfully.
[root@cvm-da-datasvr-whd1 temp]# ll /etc/ambari-server/cert/
total 0

```
导入证书

```shell
[root@cvm-da-datasvr-whd1 temp]# ambari-server setup-security
Using python  /usr/bin/python
Security setup options...
===========================================================================
Choose one of the following options:
  [1] Enable HTTPS for Ambari server.
  [2] Encrypt passwords stored in ambari.properties file.
  [3] Setup Ambari kerberos JAAS configuration.
  [4] Setup truststore.
  [5] Import certificate to truststore.
===========================================================================
Enter choice, (1-5): 5
Do you want to configure a truststore [y/n] (y)? y
Do you want to import a certificate [y/n] (y)? y
Please enter an alias for the certificate: hd.cer
Enter path to certificate: /data/temp/ca.cer
Ambari Server 'setup-security' completed successfully.
```

```
[root@cvm-da-datasvr-whd1 temp]# ambari-server restart
Using python  /usr/bin/python
Restarting ambari-server
Waiting for server stop...
Ambari Server stopped
Ambari Server running with administrator privileges.
Organizing resource files at /var/lib/ambari-server/resources...
Ambari database consistency check started...
Server PID at: /var/run/ambari-server/ambari-server.pid
Server out at: /var/log/ambari-server/ambari-server.out
Server log at: /var/log/ambari-server/ambari-server.log
Waiting for server start.............................
Server started listening on 8080

DB configs consistency check: no errors and warnings were found.
```



visudo修改，如果用root启动的ambari server不用添加这个步骤
添加如下行
```
ambari  ALL=/usr/bin/*,/sbin/*,/bin/*,/usr/sbin/*,!/usr/bin/passwd root,!/usr/bin/su


Defaults exempt_group = ambari
Defaults !env_reset,env_delete-=PATH
Defaults: ambari !requiretty
```


启动keberos需要用到的信息：
```
10.95.10.122
WESURE.CN
ldaps://10.95.10.122:636
OU=servers_hadoop,DC=wesure,DC=cn
wesure.cn,.wesure.cn
hadoop_ldap@WESURE.CN
uHij%DvF

OU=微民保险,DC=wesure,DC=cn;OU=Service_Account,DC=wesure,DC=cn;OU=servers_hadoop,DC=wesure,DC=cn


yum install sssd sssd-ad krb5-workstation authconfig oddjob-mkhomedir -y
```




```
[root@cvm-da-datasvr-whd1 temp]# ll $JAVA_HOME/jre/lib/security
total 168
-rw-r--r-- 1 root root   4054 Jun 22  2016 blacklist
-rw-r--r-- 1 root root   1273 Jun 22  2016 blacklisted.certs
-rw-r--r-- 1 root root 112860 Jun 22  2016 cacerts
-rw-r--r-- 1 root root   2466 Jun 22  2016 java.policy
-rw-r--r-- 1 root root  26224 Jun 22  2016 java.security
-rw-r--r-- 1 root root     98 Jun 22  2016 javaws.policy
-rw-r--r-- 1 root root   3527 Jun 22  2016 local_policy.jar
-rw-r--r-- 1 root root      0 Jun 22  2016 trusted.libraries
-rw-r--r-- 1 root root   3026 Jun 22  2016 US_export_policy.jar
[root@cvm-da-datasvr-whd1 temp]# unzip -o -j -q jce_policy-8.zip -d   $JAVA_HOME/jre/lib/security
[root@cvm-da-datasvr-whd1 temp]# ll $JAVA_HOME/jre/lib/security
total 176
-rw-r--r-- 1 root root   4054 Jun 22  2016 blacklist
-rw-r--r-- 1 root root   1273 Jun 22  2016 blacklisted.certs
-rw-r--r-- 1 root root 112860 Jun 22  2016 cacerts
-rw-r--r-- 1 root root   2466 Jun 22  2016 java.policy
-rw-r--r-- 1 root root  26224 Jun 22  2016 java.security
-rw-r--r-- 1 root root     98 Jun 22  2016 javaws.policy
-rw-rw-r-- 1 root root   3035 Dec 21  2013 local_policy.jar
-rw-r--r-- 1 root root   7323 Dec 21  2013 README.txt
-rw-r--r-- 1 root root      0 Jun 22  2016 trusted.libraries
-rw-rw-r-- 1 root root   3023 Dec 21  2013 US_export_policy.jar
```


