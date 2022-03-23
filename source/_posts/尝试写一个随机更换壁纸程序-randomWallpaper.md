---
title: 尝试写一个随机更换壁纸程序-randomWallpaper
date: 2022-03-23 19:20:48
tags:
    - Python
    - Ubuntu
    - 我的开源
toc: true
categories: 我的开源
---

{% inlineImg https://img.shields.io/badge/language-Python-blue%}
{% inlineImg https://img.shields.io/github/languages/code-size/ruxia-TJY/randomWallpaper %}
{% inlineImg https://img.shields.io/badge/-Linux-yellow?logo=linux %}
{% inlineImg https://img.shields.io/badge/-Linux-blue?logo=windows %}
{% inlineImg https://img.shields.io/badge/License-MIT-blue %}

## 安装

**方法一、** 直接`clone`仓库，使用Python3运行或自己打包。

*注意：* 如果在Windows下需要安装requirements.txt的包，linux不需要。

**方法二、** 从release界面下载可执行程序。请注意，我是使用Pyinstaller在Ubuntu20.04 x64环境下打包的，在其他环境无法使用，需要自己打包。下载完成解压，运行`install.sh`文件。


## 使用

无命令行参数，会按照config.json的配置进行运行

虽然程序支持一直运行并每隔一段时间更换壁纸，但我还是推荐在Linux上使用`cron`进行管理。

### 参数列表

`-a,--add`, 添加壁纸文件夹到程序运行的选择列表中，输入多个文件夹通过`,`和空格分割。

`-c,--config`, 在命令行中运行

`-d,--delete`,删除壁纸文件夹，输入多个文件夹通过','和空格分割

`--desktop`, 在~/.local/share/applications路径下创建randomWallpaper.desktop文件

`-h,--help`, 显示帮助

`-k,--keep`, 保持程序运行，并根据config.json的`keep-time`(s)切换壁纸

`-l,--list`, 输出壁纸文件夹列表

`-n,--clean`, 清空壁纸文件夹

`-q,quit`, 退出程序，仅在运行`-k,--keep`时才需要此选项进行退出

`-r,--run`, 按照config.json配置运行

`-v,--version`, 版本号

### 配置文件config.json

程序在运行会自动创建一个配置文件。
```json
{
    "WallpaperDir":[],
    "keep-time":3,
    "keep-flag":true
}
```
`WallpaperDir`：值为提供壁纸的文件夹，程序运行会在这些文件夹下随机选择一个文件作为壁纸。

`keep-time`: 使用`-k.--keep`参数后，间隔多少秒切换一次壁纸。

`keep-flag`:为false时，程序退出`-k,--keep`循环。

使用MIT协议开源：地址：https://github.com/ruxia-TJY/randomWallpaper