# Dockerfile

Dockerfile reference 指令 

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