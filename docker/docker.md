## 1.docker run hello-world的过程



本地如果没有hello-world镜像，则会下载一个hello-world镜像运行



## 2.Docker常用命令



### 2.1帮助启动类命令



```bash
# 启动docker
$ sudo systemctl start docker

# 停止docker
$ sudo systemctl stop docker

# 重启docker

# 开机启动
$ sudo systemctl enable docker

# 查看docker基本信息

# docker具体命令帮助文档
$ sudo docker 具体命令 --help
```



### 2.2镜像命令



```bash
# 列出本地主机上的镜像
$ docker images
# 列出本地所有的镜像(含历史镜像)
$ docker images -a
# 只显示镜像ID
$ docker images -q
```



```bash
# 搜索某个镜像的名字
$ docker search 镜像名
# 只列出N个镜像,默认25个
$ docker search --limit N 镜像名
# 拉取某个镜像
$ docker pull 镜像名
# 拉去某个镜像并指定版本(tag)
$ docker pull 镜像名[:TAG]
```



```bash
# 查看镜像/容器/数据卷所占的空间
$ docker system df
# 删除镜像单个
$ docker rmi 镜像名[:TAG]/镜像id
# 删除镜像多个,-f强制删除
$ docker rmi -f 镜像名1[:TAG] 
# 删除镜像全部
$ docker rmi -f $(docker images -qa)
```



### 2.3 容器命令

```bash
# 新建+启动容器
$ docker run [OPTIONS] IMAGE [COMMAND] [ARG..]
```



**OPTIONS**



- --name="新容器名称"			为容器指定一个名称
- -d        后台运行容器并返回容器ID,也即启动守护式容器
- -i         以交互模式运行容器，通常与-t同时使用
- -t         为容器重新分配一个伪输入终端，通常与-i同时使用:也即启动交互式容器
- -P        随机端口映射，大写P
- -p hostPort:containerPort        指定端口映射，小写p，如-p 8080:80



```bash
$ docker run -it ubuntu /bin/bash
# 这里我们希望有个交互式shell,因此用的是/bin/bash
```



```bash
# 列出所有正在运行的容器
$ docker ps
```



**OPTIONS**



- -a      列出当前所有正在运行的容器+历史上运行过的
- -l      显示最近创建的容器
- -n     显示最近n个创建的容器
- -q      静默模式，只显示容器编号



#### 2.3.1退出容器



1. run进入容器，exit退出，**容器停止**



```bash
$ exit
```



1. run进入容器，ctrl+p+q退出**,容器不停止**



#### 2.3.2启动已经停止运行的容器*



```bash
$ docker start 容器ID或者容器名
```



#### 2.3.3停止容器



```bash
$ docker stop 容器ID或容器名
```



#### 2.3.4强制停止容器



```bash
$ docker kill 容器ID或容器名
```



#### 2.3.5删除已经停止的容器



```bash
$ docker rm 容器ID或容器名
```



需要删除未停止的容器需要加上参数-f



**一次性删除多个容器实例 danger!**



```bash
$ docker rm -f $(docker ps -a -q)
# or
$ docker ps -a -q | xargs docker rm
```



#### 2,3,6一些重要问题



启动守护式容器



```bash
$ docker run -d ubuntu
```



问题：然后使用docker ps -a查看发现容器已经退出，原因是
**docker容器后台运行，就必须有一个前台进程**，容器运行的命令如果不是那些一直挂起的命令，就会自动退出
因此有些镜像-d参数是不起作用的，使用-it运行一个交互式的



```bash
$ docker run -it redis:6.0.8
$ docker run -d redis:6.0.8
```



#### 2.3.7查看某个容器的日志



```bash
$ docker logs 容器ID
```



#### 2.3.8查看容器内运行的进程



```bash
$ docker top 容器ID
```



#### 2.3.9查看容器内部细节



```bash
$ docker inspect 容器id
```



#### 2.3.10进入正在运行的容器并以命令行交互



```bash
# 进入正在运行的容器并以命令行交互
$ docker exec -it 容器ID /bin/bash
# 重新进入
$ docker attach 容器ID
```



**区别**



- attach 直接进入容器启动命令的终端，不会启动新的进程，用exit退出，会直接导致容器停止
- exec 是在容器中直接打开新的终端，并且可以启动新的进程，用exit退出，不会导致容器的停止



因此一般来说我们会先使用docker run -d然后使用docker exec -it



#### 2.3.11从容器内拷贝文件到主机上



```bash
$ docker cp 容器ID:容器内路径 目的主机路径
# example
$ docker cp ddd1abihiin87:/tmp/a.txt ~/Desktop
```



#### 2.3.12导入和导出容器



```bash
# export 导出容器的内容流作为一个tar归档文件
$ docker export 容器ID > abc.tar
# export 从tar包中的内容创建一个新的文件系统再导入为镜像
$ cat abc.tar | docker import - 镜像用户/镜像名:镜像版本号
# example
$ cat abc.tar | docker import - atguigu/ubuntu3.7
```



## 3.Docker镜像



#### 3.1镜像是什么



docker的镜像实际上是由一层一层的文件系统组成，这种层级的文件系统UnionFS



#### 3.2UnionFS(联合文件系统)



Union文件系统(UnionFS)是一种分层，轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层一层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像(没有父镜像),可以制作各种具体的应用镜像



#### 3.3Docker镜像加载原理



- bootfs(boot file system)主要包含bootloader和kernel
  bootloader主要是加载kernel,Linux刚启动时会加载bootfs文件系统,在Docker镜像的最底层是引导文件系统bootfs.这一层与我们典型的Linux/Unix文件系统是一样的,包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核,此时系统也会卸载bootfs.
- rootfs(root file system)
  在bootfs之上。包含的就是典型Linux系统中的/dev,/proc,/bin,/etc等标准目录和文件.rootfs就是各种不同操作系统的发行版，比如Ubuntu,CentOS等







对于一个精简的OS,rootfs可以很小,只需要包括最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel,自己只需要提供rootfs就行了。由此可见对于不同的Linux发行版,bootfs基本上是一致的,rootfs会有差别，因此不同的发行版可以公用bootfs



镜像分层最大的一个好处就是共享资源，方便复制迁移，就是为了复用



比如说有多个镜像都从相同的base镜像构建而来，那么Docker Host只需在磁盘上保存一份base镜像:同时内存中也只需加载一份base镜像,就可以为所有容器服务.而且镜像的每一层都可以被共享。



#### 3.4重点理解



Docker的镜像层都是只读的，容器层是可写的
当容器启动时，一个新的科协曾被加载到镜像的顶部，这一层通常被称作"容器层",容器层之下的都叫做镜像层



#### 3.5发布镜像



docker commit 提交容器副本使之成为一个新的镜像



```bash
$  docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]
# example
$ docker commit -m="vim add ok" -a="zhaohang" fcbfaingngi5 hnu/myubuntu:1.3
```



之后可以发布镜像到dockerhub或者阿里云(共有库),也可以发布到私有库(相当于gitlab与github的关系)



https://cn.bing.com/search?q=doker如何构建私有Hub&qs=n&form=QBRE&sp=-1&lq=0&pq=doker如何构建私有hub&sc=0-14&sk=&cvid=3005BB22809F420AA7DF1C31A5A20ABA&ghsh=0&ghacc=0&ghpl=



## 4.Docker容器数据卷



docker 挂载主机目录访问如果出现cannot open directory: Permisssion denied，要在挂载目录后多加一个--privileged=true参数即可
使用该参数,container内的root拥有真正的root权限,否则，container内的root只是外部的一个普通用户权限



```bash
$ docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录 镜像名/镜像ID
# example
$ docker run -d -p 5000:5000 -v /zzyyuse/myregistry/:/tmp/registry --privileged=true registry
```



默认情况下，仓库被创建在容器的/var/lib/registry目录下,建议自行用容器卷映射，方便宿主机联调



卷就是目录或文件存在于一个或多个容器中,由docker挂载到容器,但不属于联合文件系统，因此能够绕过Union File System提供一些用户持续存储或共享数据的特征。



卷的设计目的就是**数据的持久化**,完全独立于容器的生存周期，因此Docker不会在容器删除时删除其挂在的数据卷。



注意：容器卷的更改不会包含在镜像的更新中,数据卷的生命周期一直持续到没有容器使用它为止。



一些例子



```bash
$ docker run -it --privileged=true -v /tmp/host_data:/tmp/docker_data --name=u1 ubuntu
```



### 读写规则映射添加说明



**只读权限，容器实例内部被限制，只能读不能写**



```bash
$ docker run -it --privileged=true -v 宿主机绝对路径目录:容器内目录:ro 镜像名
```



**卷的继承和共享**



```bash
$ docker run -it --privileged=true --volumes-from u1 --name u2 ubuntu
$ docker run -it --privileged=true --volumes-from=u1 --name=u2 ubuntu
```



如果volumes-from的主机没了，该主机还是挂载的父主机的卷



## 5,Docker常规安装简介



docker拉去某些软件可能会出一些问题，比如mysql字符集配置



### mysql



```bash
$ docker pull mysql:8.0.20
$ docker run -d -p 3306:3306 --privileged=true -v /root/mysql/log:/var/log/mysql -v /root/mysql/data:/var/lib/mysql -v /root/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:8.0.20
$ cd /root/mysql/conf
$ touch my.cnf
$ vim my.cnf
```



mysql配置(貌似8.0以上不用配置也行)



```toml
[client]
default_character_set=utf8
[mysqld]
collation_server = utf8_general_ci
character_set_server = utf8
```



```bash
$ docker restart mysql
$ docker exec -it mysql /bin/bash
$ mysql -uroot -p
$ SHOW VARIABLES LIKE 'character%'
```



挂载卷后数据持久化



### redis集群配置,扩容,缩容等



redis还没有系统性地学习
...



## 6.Docker File解析



### 6.1 Docker File是什么



Dockerfile是用来构建Docker镜像的文本文件,是由一条条构建镜像所需的指令和参数构成的脚本。



### 6.2 Docker File 内容基础知识



1. 每条保留字指令都必须为大写字母且后面要跟随至少一个参数
2. 指令按照从上到下顺序执行
3. \#表示注释
4. 每条指令都会创建一个新的镜像层并对镜像进行提交



### 6.3 Docker执行DockerFile的大致流程



1. docker从基础镜像运行一个容器
2. 执行一条指令并对容器做出修改
3. 执行类似docker commit的操作提交一个新的镜像层
4. docker再基于刚提交的镜像运行一个新的容器
5. 执行dockerfile中的下一条指令知道所有指令都完成



### 6.4 Docker File保留字



#### 6.4.1 FROM



基础镜像，当前镜像是基于哪个镜像的，指定一个已经存在的镜像作为模板，第一条必须是from



#### 6.4.2 MAINTAINER



镜像维护者的姓名和邮箱地址



#### 6.4.3 RUN



容器构建时所需要运行的命令
两种格式:shell格式和exec格式
RUN是在docker build时运行



#### 6.4.4 EXPOSE



当前容器对外暴露出的端口



#### 6.4.5 WORKDIR



指定在创建容器后，终端默认登陆进来的工作目录，一个落脚点



#### 6.4.6 USER



指定该镜像以什么样的用户去执行，如果都不指定，默认是root



#### 6.4.7 ENV



用来构建镜像过程中设置的环境变量



#### 6.4.8 ADD



将宿主机目录下的文件拷贝进镜像切回自动处理URL和解压tar压缩包



#### 6.4.9 COPY



类似ADD,拷贝文件和目录到镜像中。将从构建上下文目录中<源路径>的文件/目录复制到新的一层的镜像内的<目标路径>
COPY src dest
COPY ["src","dest"]



#### 6.4.10 VOLUME



容器数据卷，用于数据保存和持久化工作



**volume和run -v的区别，什么时候需要使用volume**

容器运行时应该尽量保持容器存储层不发生写操作，对于数据库类需要保存动态数据的应用，其数据库文件应该保存于卷(volume)中。为了防止运行时用户忘记将动态文件所保存目录挂载为卷，在Dockerfile 中，我们可以事先指定某些目录挂载为匿名卷，这样在运行时如果用户不指定挂载，其应用也可以正常运行，不会向容器存储层写入大量数据。



那么Dockerfile中的VOLUME指令实际使用中是不是就是跟docker run中的-v参数一样是将宿主机的一个目录绑定到容器中的目录以达到共享目录的作用呢？

并不然，其实**VOLUME指令只是起到了声明了容器中的目录作为匿名卷，但是并没有将匿名卷绑定到宿主机指定目录的功能**。

当我们生成镜像的Dockerfile中以Volume声明了匿名卷，并且我们以这个镜像run了一个容器的时候，docker会在安装目录下的指定目录下面生成一个目录来绑定容器的匿名卷（这个指定目录不同版本的docker会有所不同），我当前的目录为：/var/lib/docker/volumes/{容器ID}。



**总结**： volume只是指定了一个目录，用以在用户忘记启动时指定-v参数也可以保证容器的正常运行。比如mysql，你不能说用户启动时没有指定-v，然后删了容器，就把mysql的数据文件都删了，那样生产上是会出大事故的，所以mysql的dockerfile里面就需要配置volume，这样即使用户没有指定-v，容器被删后也不会导致数据文件都不在了。还是可以恢复的。



**volume指定的位置在容器被删除以后数据文件会被删除吗**

volume与-v指令一样，容器被删除以后映射在主机上的文件不会被删除。



**如果-v和volume指定了不同的位置，会发生什么事呢？**

会以-v设定的目录为准，其实volume指令的设定的目的就是为了避免用户忘记指定-v的时候导致的数据丢失，那么如果用户指定了-v，自然而然就不需要volume指定的位置了。



其实一般的dockfile如果不是数据库类的这种需要持久化数据到磁盘上的应用，都是无需指定volume的。指定volume只是为了避免用户忘记指定-v时导致的数据全部在容器中，这样的话容器一旦被删除所有的数据都丢失了。

**那么为什么dockerfile中不提供一个能够映射为主机目录:容器目录这样的指令呢？**其实这样的设计是有道理的，如果在dockerfile中指定了主机目录，这样dockerfile就不具备了可移植性了，毕竟每个人所需要映射的目录可能是不同的，那么最好的办法就是把这个权利交给每个运行这个dockerfile的人，所以才会有 run -v 主机目录:容器目录 这样的指令。



————————————————

版权声明：本文为CSDN博主「诺浅」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/qq32933432/article/details/120944205



```bash
$ docker volume create edc-nginx-vol // 创建一个自定义容器卷
$ docker volume ls // 查看所有容器卷
$ docker volume inspect edc-nginx-vol // 查看指定容器卷详情信息
$ docker volume rm edc-nginx-vol // 删除自定义数据卷
```



**docker volume命令**

https://blog.csdn.net/lihongbao80/article/details/122812274

#### 6.4.11 CMD



- 指定容器启动后要干的事情
  和RUN类似也是shell和exec两种格式
- Dockerfile中可以由多个CMD指令,但只有最后一个生效,CMD会被docker run之后的参数替换



例子:
tomacat 的Dockerfile中：



```dockerfile
EXPOSE 8080
CMD ["catalina.sh","run"]
```



如果我们这样启动tomcat:



```bash
$ docker run -it -p 8080:8080 57800e5b1cbf /bin/bash
```



/bin/bash这个参数会覆盖CMD参数,从而导致无法tomacat没有正常启动



- 和RUN的区别
- run 是在 docker build 构建镜像时, 会执行的命令
- cmd 是在 docker run 启动容器时, 会执行的命令



#### 6.4.12 ENTRYPOINT



也是用来指定一个容器启动时要运行的命令,类似于CMD命令但是**ENTRYPOINT**不会被docker run后面的命令覆盖，而且这些命令行参数会被当做参数送给**ENTRYPOINT**指令的程序



```dockerfile
FROM nginx

ENTRYPOINT ["nginx","-c"] # 定参
CMD ["/etc/nginx/nginx.conf"] # 变参
```



按照dockerfile编写执行



```bash
$ docker run nginx:test
# 实际衍生出的命令
$ nginx -c /etc/nginx/nginx.conf
```



主动传参运行



```bash
$ docker run nginx:test -c /etc/nginx/new.conf
# 实际衍生出命令
$ nginx -c /ect/nginx/new.conf
```



### 6.5 案例



#### 6.5.1 自定义镜像centosjava8



要求:Centos7镜像具备vim+ifconfig+jdk8



```dockerfile
FROM centos
MAINTAINER zhaohang<zhaohang15639535703.proton.me>

ENV MYPATH /usr/local
WORKDIR $MYPATH
# 安装vim编辑器
RUN yum -y install vim
# 安装ifconfig命令查看网络ip
RUN yum -y install net-tools
# 安装java8及lib库
RUN yum -y install glibc.i686
RUN mkdir /usr/local/java
# ADD 是相对路径jar，把jdk-8u171-linux-x64.tar.gz添加到容器中,安装包必须要和Dockerfile文件在同一位置,它会自动解压
ADD jdk-8u171-linux-x64.tar.gz /usr/local/java/
# 配置java环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
ENV PATH $JAVA_HOME/bin:$PATH

EXPOSE 80
CMD echo $MYPATH
CMD echo "success-----------ok"
CMD /bin/bash
```



- 构建



```bash
$ docker build -t 新镜像名字:TAG .
```



(注意后面有个.)



#### 6.5.2 虚悬镜像(dangling image)



- 是什么
  仓库名,标签都是的镜像,可能是Dockerfile编写的问题或者docker在构建出了问题导致
- 查看所有的虚悬镜像



```bash
$ docker image ls -f dangling=true
```



- 删除虚悬镜像



```bash
$ docker image prune
```



## 7.Docker微服务实战



1. 通过IDEA新建一个普通微服务模块
2. 通过Dockerfile发布微服务部署到docker容器



```dockerfile
FROM java:8
MAINTAINER zhaohang<zhaohang15639535703.proton.me>
# VOLUME指定临时文件目录为/tmp,在主机/var/lib/docker目录下创建了一个临时文件并链接到容器的/tmp
VOLUME /tmp
# 将jar包添加到容器中并更名为zzyy_docker.jar
ADD docker_boot-0.0.1-SNAPSHOT.jar zzyy_docker.jar
# 运行jar包
RUN bash -c 'touch /zzyy_docker.jar'
ENTRYPOINT ["java","-jar","/zzyy_docker.jar"]
#暴露端口6001端口作为微服务
EXPOSE 6001
```



```bash
$ docker build -t zzyy_docker:1.6 .
```



## 8.Docker网络



启动docker后会产生一个名为docker0的虚拟网桥



```bash
# 查看docker所有网络
$ docker network ls
# 创建一个网络
$ docker network create aa_network
# 删除一个网络
$ docker network rm aa_network
# 查看网络源数据
$ docker network inspect bridge
```



docker默认创建的3大网络模式:



- bridge
- host
- none



docker的5大网络模式:



- bridge
  为每一个容器分配,设置IP等,并将容器连接到一个docker0
  虚拟网桥，默认为该模式
  使用--network bridge指定,默认使用docker0
- host
  容器将不会虚拟出自己的网卡,培植自己的IP等,二十使用宿主机的IP和端口
  使用--network host指定
- none
  容器有独立的Network namespace,但没有对其进行任何网络设置,如分配veth pair和网桥连接,IP等
  使用--network none指定
- container
  新创建的容器不会创建自己的网卡和配置自己的IP,而是和一个指定的容器共享IP,端口范围
  使用--network container:NAME或容器ID
- ...



docker网络能干什么:



- 容器间的互联和通信以及端口映射
- 容器IP变动时候可以通过服务名直接网络通信而不受到影响





网桥docker0创建一对对等虚拟设备接口,一个叫veth，另一个叫eth0，成对匹配



**host模式下使用和主机相同的网段**



```bash
# 警告
$ docker run -d -p 8083:8080 --network host --name tomcat83 billygoo/tomcat8-jdk8
# 正确
$ docker run -d --network host --name tomcat83 billygoo/tomcat8-jdk8
```



如果使用host模式，端口号会以主机端口号为主，重复时则递增



**none模式**:
该模式下,并不为Docker容器进行任何网络配置
也就是说，这个Docker容器没有网卡,IP,路由等信息,只有一个Io
需要我们自己为Docker容器添加网卡,配置IP等



**container模式**



```bash
$ docker run -d -p 8085:8080 --name tomcat85 billygoo/tomcat8-jdk8
# 下面运行这条命令会报错
$ docker run -d -p 8086:8080 --network container:tomcat85 --name tomcat86 billygoo/tomcat8-jdk8
```



相当于tomcat86和tomcat85公用同一个ip同一个端口,导致端口冲突,因为tomcat需要端口8080，因此这种情况下container模式不太适合



```bash
$ docker run -it --name=alpine1 alpine /bin/sh
$ docker run -it --network container:alpine1 --name=alpine2 alpine /bin/sh
# 进入到容器,查看网络
$ ip addr
```



如果关闭了alpine1,alpine2的网络就没有了



**自定义网络案例**



1. 自定义桥接网络，自定网络默认使用的是桥接网络bridge



```bash
$ docker network create zzyy_network
```



1. 新建自定义网络



```bash
$ docker run -d -p 8081:8080 --network zzyy_network --name tomcat81 billygoo/tomcat8-jdk
$ docker run -d -p 8082:8080 --network zzyy_network --name tomcat82 billygoo/tomcat8-jdk
$ docker exec -it tomcat81 bash
$ ip addr
$ ping tomcat82
$ exit
$ docker exec -it tomcat82 bash
$ ip addr
$ ping tomcat81
$ exit
```



1. 新建容器加入上一步新建的自定义网络
2. 互相ping测试



## 9.docker-compose

Compose是Docker公司推出的一个工具软件，可以管理多个Docker容器组成一个应用。需要定义一个YAML格式的配置文件**docker-compose.yml，**写好多个容器之间的调用关系。然后，只要一个命令，就能同时启动/关闭这些容器。



### 9.1 compose核心概念

- 一文件

docker-compose.yml

- 两要素

1. 服务(serivce)

一个个应用容器实例，比如订单微服务，库存微服务，mysql容器，nginx容器或者redis容器

1. 工程(project)

由一组关联的应用容器组成的一个完整业务单元，在docker-compose.yml文件中定义



### 9.2 compose使用的3个步骤

1. 编写Dockerfile定义各个微服务应用并且构建出对应的镜像文件
2. 使用docker-compose.yml定义一个完整业务单元，安排好整体应用中的各个容器服务
3. 最后，执行docker-comose up命令，来启动并运行整个应用程序，完成一键部署上线

### 9.3 compose常用命令

```bash
$ docker-compose -h #查看帮助
$ docker-compose up #启动所有docker-compose服务
$ docker-compose up -d #启动所有的docker-compose服务并且后台运行
$ docker-compose down #停止并且删除容器,网络,卷,镜像
$ docker-compose exec yml里面的服务id # 进入容器实例内部 docker-compose exec docker-compose.yml文件中的服务id /bin/bash
$ docker-compose ps #展示当前docker-compose编排过的运行的所有容器
$ docker-compose top #展示当前docker-compose编排过的容器进程
$ docker-compose logs yml里面的服务id #查看容器输出日志
$ docker-compose config #检查配置
$ docker-compose config -q #检查配置，有问题才有输出
$ docker-compose restart #重启服务
$ docker-comopse start #启动服务
$ docker-compose stop #停止服务
```

### 9.4 compose编排微服务

1. 编写docker-compose.yml

contianer_name设定容器名称

```yaml
version: "3"

services:
	microService:
  	image: zzyy_docker: 1.6
    contianer_name: ms01
	  ports:
  	  -"6001:6001"
    volumes:
      - /app/microService:/data
    networks:
      - atguigu_net
    depends_on:
      - redis
      - mysql

	redis:
		image: redis:6.0.8
		ports:
  		- "6379:6379"
  	volumes:
    	- /app/redis/redis.conf:/etc/redis/redis.conf
    	- /app/redis/data:/data
    networks:
      - atguigu_net
    command: redis-server /etc/redis/redis.conf
  
  mysql: 
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: '123456'
      MYSQL_ALLOW_EMPTY_PASSWORD: 'no'
      MYSQL_DATABASE: 'db2021'
      MYSQL_USER: 'zzyy'
      MYSQL_PASSWORD: 'zzyy123'
    ports:
      - "3306:3306"
    volumes:
      - /app/mysql/db:/var/lib/mysql
      - /app/mysql/conf/my.cnf:/etc/mysql/conf.d/my.cnf
      - /app/mysql/init:/docker-entrypoint-initdb.d
    networks:
      - atguigu_net
    command: --default-authentication-plugin=mysql_native_password #解决外部无法访问

networks:
	atguigu_net:
      
```

2.修改application.yml中固定写死的ip换成docker服务名

比如:

```yaml
spring:
	datasource:
  	url: jdbc:mysql://mysql:3306/db2021?useUnicode=true&characterEncoding=utf-8&useSSL=false
```

1. 编写Dockerfile将打包好的jar包放到Dockerfile同级目录下

```bash
$ docker build -t zzyy_docker:1.6 .
```

1. 使用docker-compose

```bash
# 检查yaml文件的语法
$ docker-compose config -q
# 执行,-d表示后台运行
$ docker-compose up -d
```

运行完成后检查network

```bash
$ docker network ls
```

发现其网络名加了前缀后缀,服务名也加了前缀和后缀





## 10.Docker轻量级可视化工具Portainer

https://www.portainer.io/

## 11.Docker容器监控之CAdvisor+InfluxDB+Granfana(CIG)

### 11.1 原生命令

```bash
$ docker stats
```

可以很方便的看到当前宿主机上所有的容器的CPU，内存以及网络流量等数据，但是docker stats统计结果只能是当前宿主机的全部容器,数据资料是实时的,没有地方存储,没有健康指标过线预警功能

### 11.2 CAdvisor监控收集+InfluxDB存储数据+Granfana展示图表

1. CAdvisor

CAdvisor是一个容器资源监控工具，包括容器的内存,CPU,网络IO,磁盘IO等监控，同时提供了一个WEB页面用于查看容器的实时运行状态。CAvisor默认存储2分钟的数据，而且只是针对单物理机。不过CAdivisor提供了很多数据集成的接口,支持InfluxDB,Redis,Kafka等集成,可以加上对应配置将监控数据发往这些数据库存储起来



CAdvisor功能主要有两点:

- 展示Host和容器的两个层次的监控数据
- 展示历史变化数据



1. InfluxDB

InfluxDB是用Go语言编写的一个开源分布式时序，事件和指标数据库，无需外部依赖。CAdivisor本身已经提供了InfluxDB的集成方法，在启动容器时指定配置即可



InfluxDB的主要功能:

- 基于时间序列，支持与事件有关的相关函数(如最大,最小,求和等)
- 可度量性: 你可以实时对大量数据进行计算
- 基于事件: 它支持任意的事件数据



1. Granfana

Granfana是一个开源的数据架空分析可视化平台,支持多种数据源配置(支持的数据源包括InfluxDB,MYSQL,Elasticsearch,OpenTSDB,Graphite等)和丰富的插件以及模板功能,支持图表权限控制和警报



Granfana主要特性:

- 灵活丰富的图形化选项
- 可以混合多种风格
- 支持白天和黑夜模式
- 多个数据源



三件套容器编排

```yaml
version: '3.1'

 

volumes:

  grafana_data: {}

 

services:

 influxdb:

  image: tutum/influxdb:0.9

  restart: always

  environment:

    - PRE_CREATE_DB=cadvisor

  ports:

    - "8083:8083"

    - "8086:8086"

  volumes:

    - ./data/influxdb:/data

 

 cadvisor:

  image: google/cadvisor

  links:

    - influxdb:influxsrv

  command: -storage_driver=influxdb -storage_driver_db=cadvisor -storage_driver_host=influxsrv:8086

  restart: always

  ports:

    - "8080:8080"

  volumes:

    - /:/rootfs:ro

    - /var/run:/var/run:rw

    - /sys:/sys:ro

    - /var/lib/docker/:/var/lib/docker:ro

 

 grafana:

  user: "104"

  image: grafana/grafana

  user: "104"

  restart: always

  links:

    - influxdb:influxsrv

  ports:

    - "3000:3000"

  volumes:

    - grafana_data:/var/lib/grafana

  environment:

    - HTTP_USER=admin

    - HTTP_PASS=admin

    - INFLUXDB_HOST=influxsrv

    - INFLUXDB_PORT=8086

    - INFLUXDB_NAME=cadvisor

    - INFLUXDB_USER=root

    - INFLUXDB_PASS=root
```