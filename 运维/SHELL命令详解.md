## SHELL命令详解
### stat命令
stat命令用于显示文件的状态信息。stat命令的输出信息比ls命令的输出信息要更详细。
#### 语法
```
stat(选项)(参数)
```
- 选项
```
-L：支持符号连接；
-f：显示文件系统状态而非文件状态；
-t：以简洁方式输出信息；
-c: 格式化输出
--help：显示指令的帮助信息；
--version：显示指令的版本信息。
```
- 参数

文件：指定要显示信息的普通文件或者文件系统对应的设备文件名。

#### 实例
```
[root@CNSZ431014 ~]# ls -l xkm.py
-rwxr-xr-x. 1 root root 531 Oct 29  2014 xkm.py
[root@CNSZ431014 ~]# stat xkm.py
  File: `xkm.py'
  Size: 531             Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d      Inode: 143903      Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2014-10-29 15:09:31.622124256 +0800
Modify: 2014-10-29 15:33:15.126331273 +0800
Change: 2015-10-16 15:19:46.153738220 +0800
```
- File：显示文件名 
- Size：显示文件大小 
- Blocks：文件使用的数据块总数 
- IO Block：IO块大小 
- regular file：文件类型（常规文件） 
- Device：设备编号 
- Inode：Inode号 
- Links：链接数 
- Access：文件的权限 
- Gid、Uid：文件所有权的Gid和Uid。
会出现3个类型的时间，分别是Access，Modify，Change。下面我们就对这3个时间进行详细解释下。
- access time：表示我们最后一次访问（仅仅是访问，没有改动）文件的时间
- modify time：表示我们最后一次修改文件的时间
- change time：表示我们最后一次对文件属性改变的时间，包括权限，大小，属性等等。
```
[root@CNSZ431014 ~]# stat -f xkm.py
  File: "xkm.py"
    ID: 1c78937603be76ea Namelen: 255     Type: ext2/ext3
Block size: 4096       Fundamental block size: 4096
Blocks: Total: 2580302    Free: 545535     Available: 414463
Inodes: Total: 655360     Free: 430476
[root@CNSZ431014 ~]# stat -t xkm.py 
xkm.py 531 8 81ed 0 0 801 143903 1 0 0 1414566571 1414567995 1444979986 4096
```
