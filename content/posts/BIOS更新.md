---
title: 神舟笔记本更新 BIOS
date: 2022-01-25 21:36:28
categories: 
- Others
tags:
- BIOS更新
---

> 如果电脑运行正常，请不要随便更新 BIOS！

一般情况下神舟笔记本出厂后就不会提供 BIOS 更新，但是神舟笔记本都是采用蓝天（Clevo）模具，所以可以下载蓝天准系统的 BIOS 来更新神舟笔记本电脑的 BIOS。

## 下载 BIOS

首先先确定你的笔记本的模具型号，查看笔记本背面（D面）的标签上的序列号，例如我的笔记本（神舟战神Z7M-KP7S1）的序列号为: `NKN850HK1006L00933`，相应的模具型号为 `N850HK`。

知道了模具的型号后就可以下载对应蓝天模具准系统的 BIOS，现在蓝天官网也无法直接下载发布的 BIOS。不过好在有热心的网友制作了一个[镜像网站](http://repo.palkeo.com/clevo-mirror/)，在这个网站上可以下载蓝天模具的 BIOS。

> 用户名、密码均为repo

在网站上找到对应模具的 BIOS 下载页面，例如 Z7M-KP7S1 的模具型号 `N850HK` 对应的模具为 [N85xHKHJ](http://repo.palkeo.com/clevo-mirror/N85xHKHJ)。

在模具的下载页面可以看到很多 BIOS 版本，一般下载最新的 BIOS 和 EC，这里我下载的 BIOS 和 EC 分别为 [B10522.zip](http://repo.palkeo.com/clevo-mirror/N85xHKHJ/B10522.zip) 和 [EC10515.zip](http://repo.palkeo.com/clevo-mirror/N85xHKHJ/EC10515.zip)。解压到不含中文路径的文件夹。

> 注意：BIOS 只可以更新到新的版本，不可以回退版本。

## 刷写 EC

下载的 EC 包中提供了一个 bat 安装脚本，但是由于其提供的安装程序与 Windows 10 不兼容，所以需要采取另外一种方式安装。

这种方式需要使用一个工具 `ECWinFlash`。

1. 打开 `ECWinFlash` 软件。在软件界面上可以看到当前电脑的 EC 版本信息，点击 `New ROM Information` 选择我们解压后的 EC 文件夹中的文件拓展名为数字的文件且文件大小为 128 KB。
2. 选择后，会显示新的 EC 的信息，仔细检查 `New ROM Information` 和 `Old ROM Information`。确认两个 `Project Name` 相同，`EC Version` 为你想要更新的版本。
3. 确认无误后点击 Flash 按钮。刷完以后电脑后自动关机，这时建议拔掉笔记本的电源和电池，手触摸电池槽的电极，边按电源开关十秒，释放静电。然后再插上电源开机。进入 BIOS 可以看到 EC 版本已经更新。

## 刷写 BIOS

1. 打开解压后的 BIOS 文件夹，进入 ALL 目录，以管理员权限运行 `WMeset.exe` 可执行文件。电脑会自动重新启动。
2. 以管理员权限运行 PowerShell 或者 CMD，切换到 BIOS 文件夹的 ALL 目录下。执行 `FlashMeWinX64.bat` 脚本，耐心等待刷写结束，结束后系统不会自动关机，需要手动关机，建议还是和刷 EC 一样拔掉电源电池释放静电。然后再开机。
3. 电脑会启动到 `CMOS LOST POST` 界面，按下两次 Enter 键，电脑会自动重新启动。
4. 电脑重启后，以管理员权限运行 PowerShell 或者 CMD，切换到 BIOS 文件夹的 ALL 目录下。执行 `EOLWIN64.bat` 脚本，运行结果没报错即可进入下一步。
5. 执行 `EOLTESTWIN64.bat` 脚本，无错误信息即可。
6. 最后运行 `MEINFOWIN64.bat` 脚本，查看相关信息。

至此 BIOS 和 EC 的更新已经完成，重启进入 BIOS，可以看到 BIOS 和 EC 的版本都更新成功。然后按需修改 BIOS 中的相关设置。

## 已知问题

- 电脑开机的图标会变成非常丑的 Style Note，后续可以通过修改 BIOS 解决。
- Control Center 可能运行异常，到[蓝天驱动下载中心](https://www.clevo.com.tw/clevo_down.asp?lang=en)下载新的 Control Center 可以解决。

## 修改开机图标

1. 首先找到我们想要替换的图标，这里我选择神舟原版的战神图标，从神舟官方的 BIOS 中提取 BIOS 图标。
2. 打开 `ChangeLogo` 软件，选择官方 BIOS，分别选择 `default` 和 `MyOemLogo1` 保存到电脑上。
3. 然后选择我们需要更改的BIOS文件 `N850HJ8M.22`，替换 `default` 和 `MyOemLogo1`，保存更改后的 BIOS。
4. 然后将修改后的 BIOS 移动回到原来的目录下，并修改为原来的名称。
5. 由于我们修改了 BIOS，无法通过官方提供的脚本的验证，所以我们要强制刷入。以管理员权限运行 PowerShell 或者 CMD，运行如下命令 `./FPTW64.exe -f "N850HJ8M.22" -BIOS`，耐心等待刷写结束，然后关机重新启动。
6. 电脑会启动到 `CMOS LOST POST` 界面，按下两次 Enter 键，电脑会自动重新启动。
7. 电脑重启后，以管理员权限运行 PowerShell 或者 CMD，切换到 BIOS 文件夹的 ALL 目录下。执行 `EOLWIN64.bat` 脚本，运行结果没报错即可进入下一步。
8. 执行 `EOLTESTWIN64.bat` 脚本，无错误信息即可。
9. 最后运行 `MEINFOWIN64.bat` 脚本，查看相关信息。

## 参考资料

- https://www.bilibili.com/read/cv2802267
- https://post.smzdm.com/p/av7zp78n/