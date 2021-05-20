# 数据采集Docker镜像制作

1.DockerFile介绍

​		Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

2.数据采集程序目录结构

​		数据采集运行在datacollect目录下，go-data-collect 是go语言通过goland编译成的linux运行程序；configs文件夹中放的是数据采集配置文件，包括configs.xml和collect-files.sh,configs.xml配置MES服务和文件服务，collect-files.sh配置数据采集相关的共享文件目录；recordfile和zaplogs分别是程序运行时产生的采集标识文件夹和采集日志文件夹。

![image-20200927150932044](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20200927150932044.png)

3.DockerFile内容及解释

```dockerfile
FROM centos:7            	#镜像基于centos:7制作
MAINTAINER "genoany"			#镜像的作者
WORKDIR ./datacollect			#容器运行时的目录
ADD go-data-collect .			#将文件go-data-collect添加到运行目录
ADD configs ./configs			#将configs目录下所有文件添加到运行目录的configs文件夹下
ENTRYPOINT sh -c "bash configs/collect-files.sh && ./go-data-collect"							#容器每次运行时执行命令，先运行collect-files.sh脚本然后启动数据采集
```

DockerFile命令介绍参考：https://www.runoob.com/docker/docker-dockerfile.html

4.制作镜像

 docker build . -t go-data-collect:1.0.0

根据当前目录下的DockerFile文件创建一个名称和tag为go-data-collect:1.0.0的镜像

![image-20200927152617896](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20200927152617896.png)

![image-20200927152410258](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20200927152410258.png)

5.创建和启动容器

docker run --name godatacollect --privileged go-data-collect:1.0.0

根据镜像go-data-collect:1.0.0创建一个名为godatacollect的镜像，并使容器内用户有root权限，本次是因为需要collect-files.sh中要执行mount命令。

容器创建并启动成功
![image-20200927154436595](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20200927154436595.png)

容器启动成功，且collect-files.sh共享文件夹mount成功
![image-20200927154444203](C:\Users\NP\AppData\Roaming\Typora\typora-user-images\image-20200927154444203.png)

6.镜像导出

 docker save go-data-collect:1.0.0 > go-data-collect1.0.0.tar

将镜像go-data-collect:1.0.0 保存到当前目录并命名为go-data-collect1.0.0.tar

