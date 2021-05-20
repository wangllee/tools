查看IP等信息：ifconfig

![image-20191223112937661](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20191223112937661.png)

查看硬盘分区：df -h

![image-20191223112807615](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20191223112807615.png)

查看文件夹大小：du -h --max-depth=1 /var/cache/yum/x86_64/7Server

![image-20191223174149113](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20191223174149113.png)

清空命令行：Ctrl + l

创建目录：mkdir -p /wll/gitlab

文件编辑：vim /wll/gitlab/test.txt

vim教程：https://www.runoob.com/linux/linux-vim.html

命令模式(Command)，输入模式(Insert)和底线命令模式(Last line)

1. i 切换到输入模式，以输入字符。

2. x 删除当前光标所在处的字符。

3. : 切换到底线命令模式，以在最底一行输入命令。

4. :q 退出 :q! 强制(不保存)退出 :w 保存 :wq 保存退出

清理安装包缓存：yum clean all

1.清除缓存目录(/var/cache/yum)下的软件包，命令：yum clean packages

2.清除缓存目录(/var/cache/yum)下的 headers，命令：yum clean headers

3.清除缓存目录(/var/cache/yum)下旧的 headers，命令：yum clean oldheaders

4.清除缓存目录(/var/cache/yum)下的软件包及旧的headers，命令：yum clean, yum clean all (= yum clean packages; yum clean oldheaders).

gitlab运维相关命令

`*# 查看Gitlab组件状态*  sudo gitlab-ctl status`
`*# 启动Gitlab所有组件*  sudo gitlab-ctl start` 
`*# 停止Gitlab所有组件*  sudo gitlab-ctl stop` 
`*# 重启Gitlab所有组件*  sudo gitlab-ctl restart`

![image-20191223113106309](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20191223113106309.png)

`gitlab修改默认仓库`// /var/opt/gitlab/git-data/repositories
`1.修改配置文件 vim /etc/gitlab/gitlab.rb`
`git_data_dirs({"default" => { "path" => "/wll/gitlab/git-data"}})`
`2.复制已存在仓库 rsync -av /var/opt/gitlab-new/git-data/repositories /wll/gitlab/git-data`
`3.刷新配置 gitlab-ctl reconfigure`

防火墙：firewalld

 一、防火墙的开启、关闭、禁用命令

（1）设置开机启用防火墙：systemctl enable firewalld.service

（2）设置开机禁用防火墙：systemctl disable firewalld.service

（3）启动防火墙：systemctl start firewalld

（4）关闭防火墙：systemctl stop firewalld

（5）检查防火墙状态：systemctl status firewalld 

二、使用firewall-cmd配置端口

（1）查看防火墙状态：firewall-cmd --state

（2）重新加载配置：firewall-cmd --reload

（3）查看开放的端口：firewall-cmd --list-ports

（4）开启防火墙端口：firewall-cmd --zone=public --add-port=9200/tcp --permanent

　　命令含义：

　　–zone #作用域

　　–add-port=9200/tcp #添加端口，格式为：端口/通讯协议

　　–permanent #永久生效，没有此参数重启后失效

　　注意：添加端口后，必须用命令firewall-cmd --reload重新加载一遍才会生效

（5）关闭防火墙端口：firewall-cmd --zone=public --remove-port=9200/tcp --permanent

​		Failed to start firewalld.service: Unit is masked，firewalld服务被锁定，不能添加对应端口。执行命令，即可实现取消服务的锁定

```
# systemctl unmask firewalld
```
​		下次需要锁定该服务时执行
```
# systemctl mask firewalld
```

**命令名称 [命令参数] [命令对象]**

命令对象一般是指要处理的文件、目录、用户等资源，而命令参数可以用长格式（完整的选项名称），也可以用短格式（单个字母的缩写），两者分别用--与-作为前缀

在Linux系统中的命令参数有长短格式之分，长格式和长格式之间不能合并，长格式和短格式之间也不能合并，但短格式和短格式之间是可以合并的，合并后仅保留一个-（减号）即可。另外ps命令可允许参数不加减号（-），因此可直接写成ps aux的样子。

文件内容查看：

cat file.txt   #全部内容

tail -n 10 file.txt        #最后10行内容

more file.txt 	#显示一屏内容，h查看快捷键