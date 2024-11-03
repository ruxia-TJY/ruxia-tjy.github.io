---
title: urlCheck 批量检测链接是否有效的小工具
date: 2024-08-28 00:23:58
updated: 2022-04-13 22:28:50
description: 批量检测链接是否有效的小工具
tags: 
    - Python
    - 我的开源
toc: true
categories: 我的开源
---

# urlCheck

批量检测链接是否有效

{% inlineImg https://img.shields.io/badge/language-Python-blue%}
{% inlineImg https://img.shields.io/github/languages/code-size/ruxia-TJY/urlCheck %}
{% inlineImg https://img.shields.io/badge/License-MIT-blue %}

## 使用教程

在命令行中
```bash
python urlCheck.py --url https://google.com https://youtube.com
```
从文件中读取
```bash
python urlCheck.py --file test.txt
```

保存结果到文件
```bash
python urlCheck.py --file test.txt --out result.txt
```

设置保存的格式，通过`out-flag`选项，默认值是**tcu**。

```bash
python urlCheck.py --url https://github.com https://dadawdd --out result.txt --out-flag ctu
```



## 参数

```bash
-h,--help                 show this help message and exit
-url URL [URL ...]        check from string
--file FILE               check from File
--timeout default=3       set timeout
--out OUT				  output to file
--out-flag OUTGLAG        output flag,default is "tcu"
-v,-V,--Version           show program's version number and exit
```



### out-flag

仅支持**t**,**c**,**u**，默认是**tcu**，即以测试时间,测试返回值,测试链接 格式保存

| flag | description |
| :-----: | :-----: |
| t | 测试时间 |
| c | 测试返回值 |
| u | 测试链接  |

可以任意进行组合


## 下载

{% btn 'https://github.com/ruxia-tjy/urlCheck',Github链接,far fa-hand-point-right,blue larger %}

## 更新记录

{% timeline ,blue %}

<!-- timeline 2024-11-03 [0.0.1](https://github.com/ruxia-TJY/urlCheck/releases/tag/0.0.1) -->

1. 实现从命令行解析和文件解析
2. 保存结果到文件

<!-- endtimeline -->


{% endtimeline %}
