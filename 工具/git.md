## 9.1 Git
### 建立认证
1. 检查是有已有SSH密钥
```
cd ~/.ssh
ls
```
如果有id_rsa id_rsa.pub这两个文件，则无需进行第二步，直接调到第三步就行。

2. 生成SSH密钥
ssh-keygen -t rsa -C "your_email@example.com"
```
falcon@ubuntu:~$ ssh-keygen -t rsa -C "tongboyu@126.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/falcon/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/falcon/.ssh/id_rsa.
Your public key has been saved in /home/falcon/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:tqkzSevUE1n+9UpU83IMLGTInARQMQ9iHXg9v8j74ys tongboyu@126.com
The key's randomart image is:
+---[RSA 2048]----+
|      +=BO.oo    |
|     ...o+Bo .   |
|       .  oo. o..|
|         +  .. +o|
|        S... .+ +|
|      .o +o..o + |
|     ..o=  .. . .|
|     .=. .E .. . |
|     .oo   ++o.  |
+----[SHA256]-----+
```
代码参数含义： 
-t 指定密钥类型，默认是 rsa ，可以省略。 
-C 设置注释文字，比如邮箱。 
直接按三次回车，即可生成id_rsa id_rsa.pub文件。
3. 复制密钥到github账户 
复制id_rsa.pub里面的内容，然后登陆到你的github账户，在账户设置中，添加SSH密钥。

4. 测试SSH连接

$ ssh -T git@github.com
```
falcon@ubuntu:~/.ssh$ ssh -T git@github.com
Hi pokyu! You've successfully authenticated, but GitHub does not provide shell access.
```

5. 将已有github项目换成ssh
```
1. git remote rm origin
2. git remote add origin git@github.com:账户名/项目名.git
3. git push origin 
```
### 从远程库克隆
```
$ git clone git@github.com:pokyu/MyCode.git
```
**运行结果：**
```
D:\02git>git clone git@github.com:pokyu/MyCode.git
Cloning into 'MyCode'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```

### 添加文件
$ git add filename(*)
$ git commit -m "描述"
```
D:\02git\MyCode>git status
On branch master
Your branch is up-to-date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        scoket-python/

nothing added to commit but untracked files present (use "git add" to track)

D:\02git\MyCode>git add *

D:\02git\MyCode>git commit -m "add tranfer file for python"
[master 4047211] add tranfer file for python
 2 files changed, 130 insertions(+)
 create mode 100644 scoket-python/transfer-files/tfclient.py
 create mode 100644 scoket-python/transfer-files/tfserver.py

D:\02git\MyCode>git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean

D:\02git\MyCode>git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")

D:\02git\MyCode>git commit -m " "
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes not staged for commit:
        modified:   README.md

no changes added to commit

D:\02git\MyCode>git add README.md

D:\02git\MyCode>git commit -m " "
Aborting commit due to empty commit message.

D:\02git\MyCode>git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   README.md
```


### 上传
git push origin master

```
D:\02git\MyCode> git push origin master
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 1.77 KiB | 1.77 MiB/s, done.
Total 6 (delta 0), reused 0 (delta 0)
To github.com:pokyu/MyCode.git
   3c20372..4047211  master -> master
```
### 更新到最新版本
git pull

### 查看某个远程仓库的详细信
可以通过命令 git remote show [remote-name] 查看某个远程仓库的详细信息，比如要看所克隆的 origin 仓库，可以运行：
```
$ git remote show origin
```
除了对应的克隆地址外，它还给出了许多额外的信息。它友善地告诉你如果是在 master 分支，就可以用git pull 命令抓取数据合并到本地。另外还列出了所有处于跟踪状态中的远端分支。

### Git pull 强制覆盖本地文件
```
git fetch --all  
git reset --hard origin/master 
git pull
```
第一个命令的意思是取回远程端所有修改;
第二句的意思是将版本号置到master这个版本上。
示例：
```
falcon@ubuntu:/opt/app/note$ git fetch --all 
Fetching origin
falcon@ubuntu:/opt/app/note$ git reset --hard origin/master 
HEAD is now at 4cbba0c init gitbook
falcon@ubuntu:/opt/app/note$ git pull
Already up-to-date.
```


## 报错
### fatal: unable to auto-detect email address...
Probably a typo mistake: set user.mail with no e. Fix it by setting user.email in the Global Configuration with
```
falcon@ubuntu:/opt/app/note$ git commit -m " "

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'falcon@ubuntu.(none)')

falcon@ubuntu:/opt/app/note$ git config --local -l
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
remote.origin.url=git@github.com:pokyu/note.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master

falcon@ubuntu:/opt/app/note$ git config --global user.email "tongboyu@126.com"

falcon@ubuntu:/opt/app/note$ git config --local -l
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
remote.origin.url=git@github.com:pokyu/note.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master

falcon@ubuntu:/opt/app/note$ git commit -m "add gitbook"
[master c8b53ee] add gitbook
 2 files changed, 6 insertions(+)
 create mode 100644 README.md
 create mode 100644 SUMMARY.md
falcon@ubuntu:/opt/app/note$ 
```
