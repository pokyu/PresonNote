---
title: Kerberos管理
category: Operations Guide
---

### Kerberos账号管理
#### 新建Kerberos账号

新建账号使用如下
```
addprinc USERNAME
```
示例：
```
kadmin.local:  addprinc jessetong
WARNING: no policy specified for jessetong@HADOOP.COM; defaulting to no policy
Enter password for principal "jessetong@HADOOP.COM": 
Re-enter password for principal "jessetong@HADOOP.COM": 
Principal "jessetong@HADOOP.COM" created.
kadmin.local:  !
```
#### 更改密码
```
kpasswd USERNAME
```
示例：
```
[osuser@dataflow2 ~]$ kpasswd jessetong
Password for jessetong@HADOOP.COM: <-- 输入旧密码
Enter new password: <-- 输入新密码
Enter it again: <-- 再次输入新密码
Password changed.
```

#### 列出所有Kerberos账号
```
listprincs
```
