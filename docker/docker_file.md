## Dockerfile命令介绍

+ **`CMD`**  
CMD指令用于指定一个容器启动时要运行的命令。
> CMD ["/bin/bash", '-l']

+ **`CENTRYPOINT`**
CENTRYPOINT指令用于指定一个容器启动时要运行的命令。  
> 如果需要，可以在运行时通过docker run的--entrypoint标志覆盖entrypoint指令。

+ **`WIRKDIR`**  
WORKDIR指令用来在从镜像创建一个新容器时，在容器内部设置一个工作目录，ENTRYPOINT和CMD指定的程序会在这个目录下执行。  
> 可以使用盖好指令为Dockerfile中后续的一系列指令设置工作目录，也可以为最终的容器设置工作目录。比如，可以如下这样为得顶的指令设置不同的工作目录。  
```
WORKDIR /opt/webapp/db
RUN bundle install
WORKDIR /opt/webapp
ENTRYPOINT ["rackup"]
```
> 可以通过`-w`标志在运行时覆盖工作目录：`sudo docker run -it -w /var/log ubuntu pwd`，该指令会将容器内的工作目录设置为/var/log

+ **`ENV`**
ENV指令用来在镜像构建过程中设置环境变量。  
```
ENV RUN_PATH=/home/rvm
```
这个新的环境变量可以在后续的指令中使用。
```
WORDIR $RUN_PATH
```
*这些环境变量会被持久保存到从我们的镜像创建的任何容器中*

+ **`USER`**
USER指令用来指定该镜像会以什么样的用户去运行。
```
USER NGINX
```
基于该镜像启动的容器会以nginx用户的身份来运行。  
我们可以指定用户名或UID以及组或GID，甚者是两者的组合。
```
USER user
USER user:group
USER uid
USER uid:gid
USER user:gid
USER uid:group
```

+ **`VOLUME`**
VOLUME指令用来向基于镜像创建的容积添加卷。
```
VOLUME ["/opt/project", "/data"]
```
> 卷可以在容器建共享和重用。
> 一个容器不是必须和洽谈容器共享卷。
> 对卷的修改是立即生效的。
> 对卷的修改不会对更新镜像产生影响。
> 卷会一直存在直到没有任何容器再使用它。

*卷功能让我们可以将数据（如源代码）、数据库或其它内容添加到容器中人而不是将这些数据提交到容器中*

+ **`ADD`**
ADD指令用来将构建环境下的文件和目录复制到镜像中。
```
ADD software.cfg /opt/application/software.cfg
```
*如果移动的文件时归档文件，那么文件Docker会自动将文件解压*

+ **`COPY`**
与ADD相似，但是对归档文件不会自动解压。


+ **`FROM`**

## 使用Dockerfile构建一个mongodb镜像
```
FROM ubuntu:16.04
# 作者信息
MAINTAINER dengjunzhen dengjunzhen@wershare.com.cn

ENV WORK_PATH /usr/local/

ENV MONGO_PACKAGE_NAME mongodb-linux-x86_64-ubuntu1604-3.4.10
# 将mongodb拷贝至工作目录
COPY ./$MONGO_PACKAGE_NAME $WORK_PATH/mongodb
# 创建数据文件目录
RUN mkdir -p /data/db
# 更新
RUN apt-get update
# 安装必要依赖
RUN apt-get install -y libssl1.0.0 libssl-dev
# 设置环境变量
ENV PATH=$WORK_PATH/mongodb/bin:$PATH
# mongodb的web端口
EXPOSE 28017
# 连接端口
EXPOSE 27017
# 启动服务，--rest参数表示开启web服务
CMD ["mongod","--rest"]
```
