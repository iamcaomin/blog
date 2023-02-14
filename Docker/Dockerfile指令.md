### Dockerfile

#### 指令
```shell
# 镜像来源 FROM
FROM <image> or <image>:<tag>

# 维护者 MAINTAINER
MAINTAINER <username>

# 运行指令 RUN
# 三种终端
# 1. 默认 shell ，即 /bin/sh -c
# 2. exec
# 3. 其他终端
#
# ps:
# 每条 RUN 指令在当前镜像中执行命令，并提交新的镜像
# 命令过长，使用/换行
RUN <command>
# or
RUN ['executable', 'param1', 'param2'] # 使用 exec 执行，或者使用其他终端

# CMD
# 每个 dockerfile 只能有一个 CMD 命令，如果指定了多条，则会最有一条命令
# 如果用户启动容器时制定了运行的命令（RUN），则会覆盖 CMD 指定的命令
CMD ['executable', 'param1', 'param2'] # 使用 exec 执行，官方推荐方式
CMD command param1 param2 # /bin/sh 执行，当需要交互时使用
CMD ['param1', 'param2'] # 提供给 ENTRYPOINT 的默认参数

# EXPOSE
# EXPOSE 22 80 8443
# 暴露端口供互联系统使用
# 容器运行时，-P 自动分配一个端口到指定端口；-p 可以具体指定本地端口
# docker run -d -p 80:80 docker/getting-started
# 指定本地的 80 端口，映射到容器的 80 端口
EXPOSE <port> [<port> ...]

# ENV
# 指定环境变量，在 RUN 中使用，并在容器运行中保持
ENV <key> <value>

# ADD
# 复制指定的 src 到容器中的 dest
# 其中 src 可以是 dockerfile 所在目录的相对路径中的文件或目录，也可以是一个 URL，或者一个 tar 文件（会自动解压成一个目录）
ADD <src> <dest>

# COPY
# 与 ADD 功能一致，区别有二：
# 1. 当目标路径不存在时，会自动创建
# 2. 当使用本地目录为源目录时，更推荐使用 COPY
COPY <src> <dest>

# ENTRYPOINT
# 配置容器启动后执行的命令，并且不可被 docker run 提供的参数覆盖
# 每个 dockerfile 中只能有一个 ENTRYPOINT ,当存在多个时，以最后一个为准
ENTRYPOINT ['executable', 'param1', 'param2']
EXTRYPOINT command param1 param2 # shell 中执行

# VOLUME
# 创建一个可以从本地主机或其他容器挂在的挂载点，一般用于存放数据路或需要保持的数据等

VOLUME ["/data"]

# USER
# 指定运行容器时的用户名或 UID，在后续的 RUN 中也会使用指定用户
# 当服务不需要管理员权限时，可以通过 USER 命令指定运行用户
# 使用 RUN 创建所需要的用户， 如 RUN groupadd -r postgres && useradd -r -g postgres postgres, 创建用户组 postgres，同时创建用户postgres，并加入该用户组
# 临时获取管理员权限，使用gosu，而不是sudo

USER deamon

# WORKDIR   
# 为 RUN、CMD、ENTRYPOINT 配置工作目录
# 如果参数是相对路径，则基于之前命令的指令的路径

WORKDIR /a
WORKDIR b
# 最终工作目录为 /a/b

# ONBUILD
# 配置当目前镜像作为其他新创建镜像的基础镜像时，所执行的指令
# image-a
 ONBUILD ADD . /app/src
 ONBUILD RUN /usr/local/bin/python-build --dir /app/src
 
 # 当镜像 image-a 作为基础镜像时，会自动执行 ONBUILD 指令
 FROM image-a
 
 ADD . /app/src
 RUN /usr/loca/bin/python-build --dir /app/src
```
