---
title: toscreen
date: 2021-08-13 10:39:44
update: 2022-05-31 16:54:05
cover: https://cdn.jsdelivr.net/gh/ruxia-TJY/BlogUse@latest/img/opensource/toscreen/toscreen.jpg
description: 基于Python+PySide6 功能是通过串口向屏幕传输处理后的图片和视频，目前支持OLED
tags:
    - C/C++
    - 我的开源
    - 上位机
    - Python
    - PySide6
    - Qt
toc: true
categories: 我的开源
---




开始开发C++版本，后期Python版本可能不在开发。

将图片从电脑通过串口显示到屏幕上，目前支持单色OLED屏幕。



## 如何使用

### CPP版本

使用Qt5.9.9进行开发，因为使用的是MinGW编译好的OpenCV，所以请使用MInGW编译Qt，如果更改为MSVC请自行编译相应的OpenCV。



### Python版本

发布版本通过pyinstaller打包。再Windows上，您可以直接下载exe程序运行，或者下载源代码，通过命令

```shell
pip install -r requirements.txt
```

安装相关包，运行toScreen.py文件。


## 下载


{% btn 'https://gitee.com/ruxia-tjy/to-screen',Gitee链接,far fa-hand-point-right,blue larger %}

## 更新记录

**0.0.0.4**更新
+ 增加了跳转指定帧和首帧功能
+ 增加视频信息显示
+ 小幅度修改界面