# Docker & Docker Compose

![image-20221126101345958](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221126101345958.png)



* Docker 介绍
* Docker 基本使用
* Dockerfile 基本使用
* Docker Compose 基本使用
* 遗留问题 depends_on 容器编排 K8S
___
**前言：软件安装**

1. 传统方式，下载安装包，运行安装包，安装软件
2. Homewebrew 命令行安装
3. docker 构建镜像，运行容器（模拟上线时的真实网络环境，上线的时候各应用的网络是隔离的）



## Docker

* Docker is an open platform for developing, shipping & running applications. 
* Package and run an application in **isolated environment** ——container
* Containers are **lightweight** and **contain everything needed** to run the application, so you **do not need to rely on what is currently installed on the host**
* **Deploy** your application into **various environment**, this works the same whether your production environment is different
* 解决了：程序打包镜像之后，便可以在任意环境下正常运行，不用担心运行环境的变化而导致应用无法正常启动。不会再出现，「程序在本地运行成功，推送到线上的服务器上无法正常运行」的问题。



![image-20221127115331067](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221127115331067.png)

* VM：From the image above, you can see each virtual machine has its guest operating system above the host operating system, which makes virtual machines heavy. 

* Docker：While on the other hand, Docker containers share the host operating system, and that is why they are lightweight.

___



1. Docker

   粗略的理解为**轻量级**的**虚拟机**软件

   虚拟机：学习 Linux 命令行的时候，在 Windows 系统创建的虚拟机软件

2. Docker 核心概念

   container 容器  ——  虚拟的计算机

   image  镜像  ——  安装操作系统的光盘

   1. 构建镜像 docker build -t application:1.0 .

   2. 根据镜像创建容器 

      docker run --name <container_name> -p 8080:8080 <image_name>:<image_version>

      docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD='pass' mysql:5.8

   ![image-20221116094056975](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221116094056975.png)

3. Docker 基本使用

    1. 运行镜像	docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD='pass' mysql:5.8
    2. 查看镜像	docker images		docker rmi <image_id>
    3. 查看容器	docker ps [-a]		  docker rm -f <docker_id>

4. 运行镜像

     docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD='pass' mysql:5.8

     从 Docker Hub 拉取镜像 image，然后根据镜像创建容器 container

5. 管理镜像

    docker images

    docker rmi <image_id>

    docker pull <image_name>

    docker push <image_name>

6. 管理容器

    docker ps [-a]

    docker rm -f <container_name>

    docker logs <container_name>	查看容器日志

    docker start <container_name>

    docker stop <container_name>

    docker attach	进入容器

7. 镜像仓库 Docker Hub

    用来存储 Docker 镜像，可以上传和下载镜像，类似 GitHub

    官方镜像格式：<repository>:<tag>

    私人镜像格式：<docker ID>/<repository>:<tag>

8. 使用 Docker 安装 MySQL 命令

    docker **run mysql:5.8**

    docker run **-e MYSQL_ROOT_PASSWORD='pass'** mysql:5.8

    docker run **-p 3306:3306** -e MYSQL_ROOT_PASSWORD='pass' mysql:5.8

    docker run **-d** -p 3306:3306 -e MYSQL_ROOT_PASSWORD='pass' mysql:5.8

    docker run **--name mysql** -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD='pass' mysql:5.8

    ![image-20221116100959580](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221116100959580.png)

9. 持久化存储

    提供独立于容器之外的持久化存储，两种方式：Bind Mounting & Data Volume

    Bind Mounting：将主机上指定的目录挂载到容器

    **-v $PWD/data:/var/lib/mysql**

10. 查看 Docker 全局信息

    docker system info

    docker system df

    docker container prune	删除所有退出状态的容器

    docker volume prune		删除未被使用数据卷

    docker image prune			删除未被使用的镜像

11. Dockerfile 基本使用

     Docker file reference：https://docs.docker.com/engine/reference/builder/

       构建自己的 Docker 镜像

       1. 编写 Dockerfile 文件
       2. 项目打包 clean & build
       3. 构建镜像 docker build -t application:1.0 **.**		（**.** 不能省略）

12. Dockerfile 文件

     ![image-20221116102600595](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221116102600595.png)

       FROM java:11												使用 JDK11 作为基础镜像

       COPY target/*.jar app.jar							拷贝 .jar 包

       ENTRYPOINT ["java","-jar","/app.jar"]		容器启动时执行

       EXPOSE 8080												 暴露端口

13. Dockerfile reference 指令 

     Dockerfile 包含用于创建镜像的指令脚本。自己构建镜像的时候，需要编写Dockerfile。

     * FROM 指定基础镜像  

     * RUN 构建image时执行命令command  

     * CMD 容器启动时执行的默认命令

       CMD 与 RUN 指令的区别：RUN 在构建镜像的时候执行，并生成一个新的镜像；CMD 在容器运行的时候执行，在构建时不进行任何操作。  

     * EXPOSE 容器运行时监听指定的网络端口  

     * ENV 设置环境变量 K-V键值对  

     * COPY 复制本地文件到容器中  

     * ADD 功能更多的复制文件  

     * ENTRYPOINT 给容器配置一个可执行程序

       ENTRYPOINT 与 CMD 非常类似，不同的是通过docker run执行的命令不会覆盖 ENTRYPOINT。

       docker run命令运行容器时 指定的参数，都会被当做参数再次传递给 ENTRYPOINT。

       Dockerfile 中只允许有一个 ENTRYPOINT 命令。  

     * VOLUME 创建挂载点，定义挂载卷  

     * WORKDIR 指定工作目录  

     * USER 指定当前用户  

     * LABEL 为镜像添加元数据，以键值对的形式  

     * ARG 指定传递给构建运行时的变量  

     * ONBUILD 为镜像设置触发指令  

     * STOPSIGNAL 设置停止容器时发送的系统调用信号  

     * HEALTHCHECK 如何测试容器检查其是否工作  

     * SHELL 设置执行shell命令

     

14. 构建镜像，运行镜像，创建容器

       构建镜像：docker build -t application:1.0 **.**

       运行镜像，创建容器：docker run --name application -d -p 8080:8080 application:1.0

15. Docker Compose 基本使用

       Docker 如何部署多容器应用？

       各容器之间如何通信？

       ![image-20221116105409490](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221116105409490.png)

       构建镜像，单独创建容器，各容器间不能通信

       **Docker 中各容器之间是资源隔离的**

     容器和容器，容器和主机host之间是隔离的。网络环境是隔离的，容器间不能进行访问。

     By default, a container is relatively well isolated from other containers and its host machine.

16. 使用 Docker Compose 部署多容器应用

      docker-compose 类似于脚本，统一执行所有 docker 命令

      1. 创建 docker-compose.yml 文件
      2. 编写 .yml 配置文件
      3. docker-compose up 统一执行 docker 命令（包含构建镜像 docker build；创建容器 docker run）
      4. docker-compose down 关闭网格中的所有容器

      **Attention：使用 docker-compose 命令需要在 docker-compose.yml 同级目录下**

      ![image-20221116111101780](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221116111101780.png)

17. Docker Compose 部署 Spring Boot + MySQL 应用
       1. 创建编写 docker-
       2. compose.yml 文件
       3. 修改 spring.datasource.url 等配置信息  **内部访问使用 docker-compose 中声明的名称作为 host**
       4. 应用重新打包		**clean & build**
       5. 删除镜像，重新构建镜像  **docker rmi <image_id>	|	docker-compose build <image_name>**
       6. 执行 docker-compose up 创建容器
       7. docker-compose down 关闭容器

18. docker-compose.yml 文件

     ![image-20221116112156378](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221116112156378.png)

      **网络管理：服务名称作为 host，进行容器间内部访问**

      **依赖管理：Spring Boot 容器的启动依赖于 MySQL 容器的启动**

      ![image-20221116112316290](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221116112316290.png)

      **docker-compose 启动的多容器应用，内部访问使用 docker-compose 声明的名称作为 host**

      ![image-20221116112547111](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221116112547111.png)

19. 修改代码或者配置文件之后，重新构建镜像，创建容器 ![image-20221116112927841](/Users/haodong.liu/Library/Application Support/typora-user-images/image-20221116112927841.png)

          1. docker-compose down	关闭容器 & 删除容器
          2. ./gradlew clear build		重新打包
          3. docker rmi <image_id>	删除镜像
          4. docker-compose up		 构建镜像 & 创建容器

20. Docker Compose 常用命令

          docker-compose up		启动应用
          
          docker-compose build		构建镜像
          
          docker-compose down	   停止并删除应用
          
          docker-compose ps		查看容器
          
          docker-compose logs <service_name>	查看服务的日志



Docker & Virtual Machine

运行在 Docker 容器上的程序直接使用的都是实际物理机的硬件资源
