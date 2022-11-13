# Docker命令

## 1.image(镜像)命令

```shell
docker images  #查看所有本地的主机上的镜像 
#可选项
 -a --all    #列出所有镜像
 -q --quiet  #只显示镜像的id

docker search 镜像的名字 #搜索镜像 
docker pull  镜像名字：版本号(TAG)(:版本号 可以不加) #下载镜像
docker rmi 镜像ID     #删除镜像
docker rmi -f  镜像ID    #强制删除镜像
docker rmi -f $(docker images -aq)   #强制删除所有镜像
```

## 2.container(容器)命令

```shell
docker run 镜像名    #新建容器并启动
#可选项
--name="Name"      给容器起名字
-d                 后台方式运行
-it /bin/bash 或者 it /bin/sh    使用交互方式运行 
-p          指定容器端口
这些命令都是在镜像名之前
如[root@VM-8-10-centos home]# docker run -it centos /bin/bash  这个就是创建一个容器并进入bash命令界面
exit    #退出并停止容器
Ctrl + p + q    #只是退出容器
docker ps    #列出所有的容器
#可选项
-a    #列出当前正在运行的容器 + 带出历史运行过的容器
-n=数字   #显示最近创建的几个容器
-q     #只显示容器的ID
docker rm 容器ID     #删除容器
docker rm -f         #强制删除容器
docker rm -f $(docker ps -aq)    #强制删除所有容器
docker start 容器ID      #启动容器
docker restart  容器ID   #重启容器
docker stop 容器ID       #停止容器
docker kill 容器ID      #强制停止容器
```

## 3.常用的命令

```shell
docker exec -it 容器ID 命令行   #进入正在运行的容器，并新建一个终端，可以在里面操作
docker attach 容器ID       #进入正在运行的容器，进入的容器是正在执行的终端，不会启动新的进程
docker cp 容器ID：容器内的文件  容器外的路径   #将指定容器内的文件拷贝到容器外的路径中
docker top 容器ID      #查看容器中进程信息
docker inspect 容器ID  #查看容器的详细信息
```

## 4.commit镜像

```shell
跟git类似，在dockerhub上也可以提交自己的镜像,提交到本地

docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[TAG]
```

## 5.数据卷

```shell
数据卷的作用就是将容器外的一个地址与容器内的一个地址联系起来，当容器外地址生成数据时，容器内对应的地址会同步数据。
#挂载数据卷的命令
docker -v 主机路径：容器路径
#匿名挂载 挂载数据卷的时候只输入容器路径，不输入主机路径，主机路径会默认生成 生成的地址可以通过inspect container 在MOUNTS那里看
docker -v 容器路径
#具名挂载 给容器里的数据卷文件一个名字 匿名挂载的数据卷文件的名字时hash值
docker -v 文件名字:容器路径

docker所有的数据卷默认在/var/lib/docker/volumes/下
```

## 6.Dockerfile

```shell 
Dockerfile用于自己制作image镜像，Dockerfile包含许多命令
FROM 镜像                  #基础镜像，一切从这里开始构建
MAINTAINER 个人信息         #镜像是谁写的
RUN <命令行命令>            #用于执行后面跟着的命令行命令,比如 yum -y install vim RUN命令会在docker build的时候执行
ADD 压缩包 <目标路径>        #将压缩包解压到目标路径
WORKDIR 路径               #镜像的工作目录
VOLUME ["路径1","路径2"]    #数据卷挂载的路径  在创建容器时可以用-v指定对应的主机的路径
EXPOSE 端口号               #暴露的端口号
CMD ["<param1>","<param2>"]          #由这个镜像生成容器时要运行的命令(docker run),多个CMD只有最有一个会生效.比如CMD["-l","-a"];
ENTRYPOINT["<param1>","<param2>"]   #跟CMD一样，只不过可以追加命令，比如docker run ···· tomcat -l 这个-l在CMD中是不行的
COPY 压缩包 <目标路径>       #将文件拷贝到目标路径时
ENV <key> <value>                      #设置环境变量

生成完Dockerfile后，就需要build生成镜像
#命令
docker build -f 文件名(如果文件名是Dockerfile，就不需要写-f 文件名) -t 镜像的名字 .(这个点一定要有)
#比如
docker build -f dockerfile01 -t diymybatis .
```

