---
title: Docker 常用命令
date: 2020-10-17
tags: 
- Docker
---

## 镜像操作

### 获取镜像

从 Docker 镜像获取镜像的命令是 `docker pull` 。其命令格式为：

```shell
docker pull [OPTIONS] NAME[:TAG]
```

例如：从仓库中获取 Ubuntu 18.04 的镜像
```shell
docker pull ubuntu:18.04
```

### 列出镜像

要想列出已经下载下来的镜像，可以使用 `docker image ls` 命令。

```shell
docker image ls [OPTIONS] [REPOSITORY[:TAG]]
```

有时候我们想只列出部分列表，`docker image ls` 有好几个参数可以帮助做到这个事情。

根据仓库名列出镜像

```bash
$ docker image ls ubuntu
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              18.04               f753707788c5        4 weeks ago         127 MB
ubuntu              latest              f753707788c5        4 weeks ago         127 MB
```

列出特定的某个镜像

```bash
$ docker image ls ubuntu:18.04
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              18.04               f753707788c5        4 weeks ago         127 MB
```

### 删除镜像

如果要删除本地的镜像，可以使用 `docker image rm` 命令，其格式为：

```shell
docker image rm [OPTIONS] IMAGE [IMAGE...]
```

比如我们有如下镜像：

```shell
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
redis               latest              84c5f6e03bf0        4 days ago          104MB
ubuntu              latest              4e2eef94cd6b        3 weeks ago         73.9MB
```

我们可以通过以下命令删除 ubuntu 镜像
```shell
$ docker image rm 4e2eef94cd6b
```


## 容器操作

### 启动容器

启动容器需要用到的命令为 `docker run` 。该命令的格式如下：

```shell
$ docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

例如：下面的命令启动了一个 ubuntu 容器，并启动了一个可交互的 shell 终端。

```shell
$ docker run -it ubuntu /bin/bash
```

- `-t` 选项让 Docker 为容器分配一个终端并绑定到容器的标准输入上。
- `-i` 选项让容器的标准输入保持打开。
- `/bin/bash` 可省略，默认为 bash。

更多的时候，需要让 Docker 在后台运行而不是直接把执行命令的结果输出在当前宿主机下。此时，可以通过添加 `-d` 参数来实现。

例如：

```shell
$ docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
b5f4da7c8211b713500ca5678b17892700f3423e9e02f46651c506bdab2bd11d
```

### 终止容器

在 Docker 中可以使用 `docker container stop` 命令来终止一个正在运行的容器。

此外，当 Docker 容器中指定的应用终结时，容器也自动终止。例如对于上面只启动了一个终端的容器，用户通过 `exit` 命令或 `Ctrl+d` 来退出终端时，所创建的容器立刻终止。

例如我们要终止我们刚才启动的容器

```shell
$ docker container stop b5f4
```

处于终止状态的容器，可以通过 `docker container start` 命令来重新启动。

```shell
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
0aca5f898c5a        ubuntu              "bash"              12 minutes ago      Exited (0) 4 minutes ago                       bold_bell

$ docker container start 0ac -i
```

此外，`docker container restart` 命令会将一个运行态的容器终止，然后再重新启动它。

### 进入容器

在启动容器时如果使用了 `-d` 参数，容器启动后就会进入后台运行。

如果我们需要进入容器中进行操作，则可以使用 `docker attach` 或者 `docker exec` 命令。

- `docker attach` 命令

```bash
$ docker attach CONTAINER_ID
```

使用 attach 命令进入容器会打开一个 shell 终端。接下来就和我们在 Linux 终端中操作相同。

> 注意：如果从这个终端中 exit，会直接终止容器。

- `docker exec`命令

`docker exec` 命令的格式和 `docker run` 命令的格式基本相同，使用 `-it` 参数即可打开一个终端

```bash
$ docker exec -it CONTAINER_ID bash
```

如果从这个 stdin 中 exit，不会导致容器的停止。所以更推荐使用 `docker exec` 命令来进入容器。

### 删除容器

使用 `docker container rm` 命令可以删除一个处于终止状态中的容器。例如

```sh
$ docker container rm CONTAINER_ID/CONTAINER_NAME
```

如果我们有很多终止中的容器，一个一个的删除会太麻烦，可以使用下面的命令一键清理所有处于终止状态中的容器

```sh
$ docker container prune
```

### 导出容器

如果要导出本地某个容器，可以使用 `docker export` 命令。

```sh
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                    PORTS               NAMES
7691a814370e        ubuntu:18.04        "/bin/bash"         36 hours ago        Exited (0) 21 hours ago                       test
$ docker export 7691a814370e > ubuntu.tar
```

### 导入容器

可以使用 `docker import` 从容器快照文件中再导入为镜像，例如

```sh
$ cat ubuntu.tar | docker import - test/ubuntu:v1.0
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
test/ubuntu         v1.0                9d37a6082e97        About a minute ago   171.3 MB
```