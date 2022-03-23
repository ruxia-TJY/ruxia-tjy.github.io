---
title: Ubuntu中PyCharm配置PySide6
date: 2022-03-12 08:59:19
tags:
    - Python
    - Ubuntu
toc: true
categories: Python
---

在PyCharm中配置PySide6网上有很多教程，步骤其实相同。

在Ubuntu中进行配置最大的问题是不知道Designer的位置。

我安装PySide6后，在命令行中使用pyside6-designer打开Designer了(笔者不记得是否为手动添加的华环境变量了，诸君可以输入试试)。

Designer的路径为
```
~/.local/bin/pyside6-designer
```

我们常用的uic/rcc等都在~/.local/bin/目录下，名称为pyside6-uic,pyside6-rcc。