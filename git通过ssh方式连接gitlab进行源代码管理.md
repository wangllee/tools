## git通过ssh方式连接gitlab

### 背景

​		最近在使用git clone，pull或push代码时经常出现一些异常，导致不能更新和提交代码，比如以下异常：

$ git clone http://192.168.1.207:9999/mes_projects_group/applicationdesigner.git
Cloning into 'applicationdesigner'...
remote: Enumerating objects: 16, done.
remote: Counting objects: 100% (16/16), done.
remote: Compressing objects: 100% (15/15), done.
error: RPC failed; curl 56 Malformed encoding found in chunked-encoding
fatal: the remote end hung up unexpectedly
fatal: early EOF
fatal: index-pack failed

$ git clone http://192.168.1.207:9999/mes_projects_group/applicationdesigner.git
Cloning into 'applicationdesigner'...
remote: Enumerating objects: 16, done.
remote: Counting objects: 100% (16/16), done.
remote: Compressing objects: 100% (15/15), done.
error: inflate: data stream error (invalid literal/lengths set)
fatal: pack has bad object at offset 934679: inflate returned -3,-5
fatal: index-pack failed

$ git clone http://192.168.1.207:9999/mes_projects_group/applicationdesigner.git
Cloning into 'applicationdesigner'...
remote: Enumerating objects: 16, done.
remote: Counting objects: 100% (16/16), done.
remote: Compressing objects: 100% (15/15), done.
fetch-pack: protocol error: bad band #104
fatal: early EOF
fatal: index-pack failed

网上提供的解决方案大多为修改或添加配置，如

git config --global http.postBuffer 524288000
git config --global core.compression -1
git config --global core.compression 0

或直接修改当前用户目录下的.gitconfig文件

[core] 
packedGitLimit = 512m 
packedGitWindowSize = 512m 
[pack] 
deltaCacheSize = 2047m 
packSizeLimit = 2047m 
windowMemory = 2047m

或是clone时加 --depth 1 等。

这里有大部分的解决方法[https://stackoverflow.com/questions/15240815/git-fatal-the-remote-end-hung-up-unexpectedly]

​		但是都不能解决问题；最后看到了这个文[https://blog.csdn.net/fastjack/article/details/79757520]的第一句话，然后我就抱着试一试的心态，尝试了一下，竟然成功了，下面就介绍这个方法。

### ssh介绍

​	SSH 为 [Secure Shell](https://baike.baidu.com/item/Secure Shell) 的缩写，由 IETF 的网络小组（Network Working Group）所制定；SSH 为建立在应用层基础上的安全协议。SSH 是较可靠，专为[远程登录](https://baike.baidu.com/item/远程登录/1071998)会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过程中的信息泄露问题。SSH最初是UNIX系统上的一个程序，后来又迅速扩展到其他操作平台。SSH在正确使用时可弥补网络中的漏洞。SSH客户端适用于多种平台。gitlab的ssh详细介绍参考https://docs.gitlab.com/ee/ssh/

### 生成sshkey

在git bash或命令行输入

`ssh-keygen -t rsa -C "2017313656@qq.com"`

连续回车即可，结果如下

Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/NP/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/NP/.ssh/id_rsa
Your public key has been saved in /c/Users/NP/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:djdVhsLZ0yarmsp07zlHzpQeVO/GiLj2t89QeYfq79U 2017313656@qq.com
The key's randomart image is:
+---[RSA 3072]----+

+----[SHA256]-----+

如果已存在，会提示，根据需要选则是否重新生成

/c/Users/NP/.ssh/id_rsa already exists.
Overwrite (y/n)?

生成的ssh相关密钥在用户目录的.ssh文件夹下

id_rsa.pub（公钥），id_rsa（私钥）

配置多个sshkeyhttps://stackoverflow.com/questions/21277806/fatal-early-eof-fatal-index-pack-failed

### 登记sshkey

1.用记事本打开id_rsa.pub（公钥），复制里面的内容

2.登录自己的gitlab，进入用户设置界面，点击SSH Keys界面。

![image-20200411143515253](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20200411143515253.png)

如图，粘贴后点击【add key】即可。

3.测试ssh连接，输入命令行 

`ssh -T git@192.168.1.207`
出现 Welcome to GitLab, @WangLeLe!表示成功

整个过程如下![image-20200411143643004](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20200411143643004.png)

### 使用ssh从gitlab服务器clone代码

git clone git@192.168.1.207:mes_projects_group/newpwrplatform.cloud.git
Cloning into 'newpwrplatform.cloud'...
remote: Enumerating objects: 382, done.
remote: Counting objects: 100% (382/382), done.
remote: Compressing objects: 100% (190/190), done.
remote: Total 15627 (delta 229), reused 342 (delta 189)
Receiving objects: 100% (15627/15627), 8.67 MiB | 12.03 MiB/s, done.
Resolving deltas: 100% (11858/11858), done.

获取成功。只有地址和http方式不同命令全部一样。