---
title: "在 Windows 上使用 Rsync"
date: 2022-11-25T20:53:15+08:00
categories: 
- Windows
- 环境配置
tags: 
- Rsync
---

Rsync 是一个用于同步和备份的 Liunx/Unix 工具软件。在上篇文章中，我使用 GitHub Actions 来部署博客，在 Actions 的最后一步使用了 Rsync 将编译生成的 HTML 文件同步到服务器的网站根目录。

如果你不想使用 GitHub Actions 自动部署，你可以手动在本地使用 Rsync 来部署。但是 Rsync 并未发布官方的二进制程序。好在 MSYS2 这个项目提供了能使用的方法。

如果你在 Widnows 上已经安装好了 Git For Windows，那么你可以通过 Git Bash 添加 Rsync 支持，否则你需要安装 MSYS2 来使用 Rsync。

> 实际上，Git For Windows 集成了 MSYS2。

## 下载 Rsync 

这里我电脑上安装了 Git For Windows，所以我将通过 Git Bash 集成 Rsync。首先需要去 [MSYS2 仓库](http://repo.msys2.org/msys/x86_64/)下载 [Rsync](https://repo.msys2.org/msys/x86_64/rsync-3.2.3-1-x86_64.pkg.tar.zst) 的可执行文件和链接库。

解压后，将 `rsync-3.2.3-1-x86_64.pkg\usr\bin` 中的文件复制到 `C:\Program Files\Git\usr\bin` 目录下。将 `rsync-3.2.3-1-x86_64.pkg\usr\lib` 中的文件复制到 `C:\Program Files\Git\usr\lib` 下。将 `rsync-3.2.3-1-x86_64.pkg\usr\share` 中的文件复制到 `C:\Program Files\Git\usr\share` 下。

这时在 Git Bash 中执行 rsync 命令仍然会提示错误：`“error while loading shared libraries: msys-zstd-1.dll: cannot open shared object file: No such file or directory occurs”.`

## 下载缺失的动态链接库

1. libzstd

在 MSYS2 的仓库中下载 [libzstd](https://repo.msys2.org/msys/x86_64/libzstd-1.4.8-1-x86_64.pkg.tar.zst)。解压后将 `msys-zstd-1.dll` 文件复制到 `C:\Program Files\Git\usr\bin` 目录下。

2. libxxhash

下载 [libxxhash](http://repo.msys2.org/msys/x86_64/libxxhash-0.8.0-1-x86_64.pkg.tar.zst)。解压后将 `msys-zstd-1.dll` 文件复制到 `C:\Program Files\Git\usr\bin` 目录下。

然后在 Git Bash 中就可以正常使用 rsync 命令。

## 参考博客

- https://shchae7.medium.com/how-to-use-rsync-on-git-bash-6c6bba6a03ca