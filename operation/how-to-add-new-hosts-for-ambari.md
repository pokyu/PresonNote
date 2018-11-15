---
title: 添加新主机
category: Operations Guide
---

## How to Add new Hosts for Ambari

### 添加hosts
```
10.60.144.16 cvm-da-datasvr-dataflow4.hadoop.com  cvm-da-datasvr-dataflow4
```

### 添加互信
```
ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.60.144.16
```
### 安装软件
```
yum install sssd sssd-ad krb5-workstation authconfig oddjob-mkhomedir -y
```

### 添加客户端主机hosts

```
10.60.16.168 cvm-da-datasvr-whd1.hadoop.com cvm-da-datasvr-whd1
10.60.16.201 cvm-da-datasvr-whd2.hadoop.com cvm-da-datasvr-whd2
10.60.16.62  cvm-da-datasvr-whd3.hadoop.com cvm-da-datasvr-whd3
10.60.16.39  cvm-da-datasvr-whd4.hadoop.com cvm-da-datasvr-whd4
10.60.16.214 cvm-da-datasvr-whd5.hadoop.com cvm-da-datasvr-whd5
10.60.16.150 cvm-da-datasvr-whd6.hadoop.com cvm-da-datasvr-whd6
10.60.16.213 cvm-da-datasvr-whd7.hadoop.com cvm-da-datasvr-whd7

10.60.16.70 cvm-da-datasvr-hdapp1.hadoop.com  cvm-da-datasvr-hdapp1
10.60.144.16 cvm-da-datasvr-dataflow4.hadoop.com  cvm-da-datasvr-dataflow4
```

```
echo "10.60.16.97 cvm-da-datasvr-riskmodel1.wesure.cn cvm-da-datasvr-riskmodel1 " >> /etc/hosts
```


### 安装jce包
```
unzip -o -j -q jce_policy-8.zip -d $JAVA_HOME/jre/lib/security
或
unzip -o -j -q jce_policy-8.zip -d /usr/local/java/jre/lib/security
```
### 拷贝msyql的驱动到对应主机
```
scp /usr/share/java/mysql-connector-java.jar cvm-da-datasvr-dataflow4:/usr/share/java/
```
注：如果不拷贝，sqoop等会报错

### 配置krb5.conf
```
[libdefaults]
  renew_lifetime = 7d
  forwardable = true
  default_realm = WESURE.CN
  ticket_lifetime = 24h
  dns_lookup_realm = false
  dns_lookup_kdc = false
  default_ccache_name = /tmp/krb5cc_%{uid}
  udp_preference_limit = 1

[domain_realm]
  wesure.cn = WESURE.CN
  .wesure.cn = WESURE.CN

[logging]
  default = FILE:/var/log/krb5kdc.log
  admin_server = FILE:/var/log/kadmind.log
  kdc = FILE:/var/log/krb5kdc.log

[realms]
  WESURE.CN = {
    admin_server = 10.95.10.122
    kdc = 10.95.10.122
  }
```

### 开始界面添加
省略


### 备注：
1.需要hadoop_ldap密码
2.需要执行hadoop.py文件