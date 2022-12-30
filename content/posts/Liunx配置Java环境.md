---
title: Liunx 配置 Java 环境
date: 2022-03-11 09:27:29
tags: 
- Java
- JDK
- Ant
- Maven
---

## 安装 JDK

到 [Oracle JDK 下载页面](https://www.oracle.com/java/technologies/downloads/)下载压缩包，并解压到 `/usr/local` 目录下: 

```sh
sudo tar -zxvf jdk-8u341-linux-x64.tar.gz -C /usr/local/
```

## 安装 Apache Ant

到 [Apache Ant 下载页面](https://ant.apache.org/bindownload.cgi)下载压缩包，并解压到 `/usr/local` 目录下：

```sh
sudo tar -zxvf apache-ant-1.10.12-bin.tar.gz -C /usr/local/
```

## 安装 Apache Maven

到 [Apache Maven 下载页面](https://maven.apache.org/download.cgi)下载压缩包，并解压到 `/usr/local` 目录下：

```sh
sudo tar -zxvf apache-maven-3.8.4-bin.tar.gz -C /usr/local/
```

## 配置环境变量

在 `/etc/profile` 中添加两行后保存，运行 `source /etc/profile` 使环境变量生效。

```sh
sudo vim /etc/profile

export JAVA_HOME=/usr/local/jdk1.8.0_341
export ANT_HOME=/usr/local/apache-ant-1.10.12
export MAVEN_HOME=/usr/local/apache-maven-3.8.4
export PATH=$PATH:$JAVA_HOME/bin:$ANT_HOME/bin:$MAVEN_HOME/bin

source /etc/profile
```

## 安装 Eclipse

到 [Eclipse 官网](https://eclipse.org/downloads)下载最新版本的 Eclipse，解压到 `/usr/local` 目录下：

```sh
sudo tar -zxvf eclipse-java-2021-12-R-linux-gtk-x86_64.tar.gz -C /usr/local/
sudo rm eclipse-java-2020-03-R-linux-gtk-x86_64.tar.gz
```


为了方便以后打开 Eclipse，我们要创建一个快捷方式，在 `/usr/share/applications` 目录下新建一个 `eclipse.desktop` 文件，文件的内容如下所示，保存后即可在 Ubuntu 的菜单中找到 Eclipse 的快捷方式。

```shell
cd /usr/share/applications
sudo vim eclipse.desktop
```

```
[Desktop Entry]
Encoding=UTF-8
Name=Eclipse
Comment=Eclipse
Exec=/usr/local/eclipse/eclipse
Icon=/usr/local/eclipse/icon.xpm
Terminal=false
StartupNotify=true
Type=Application
Categories=Application;Development;
```