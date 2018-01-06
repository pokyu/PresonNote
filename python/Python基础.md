## Python基础
### 安装
官网下载源码文件Python-2.7.12.tgz
```
tar -zxvf Python-2.7.12.tgz
cd Python-2.7.12
./configure
make
make install
mv /usr/bin/python /usr/bin/python2.6.6
ln -s /usr/local/bin/python2.7 /usr/bin/python
```
```
mkdir /opt/tmp
cd /opt/tmp
scp root@10.14.192.128:/opt/source/Python-2.7.12.tgz /opt/tmp
tar -zxvf Python-2.7.12.tgz
cd Python-2.7.12
./configure
make
make install
mv /usr/bin/python /usr/bin/python2.6.6
ln -s /usr/local/bin/python2.7 /usr/bin/python
```