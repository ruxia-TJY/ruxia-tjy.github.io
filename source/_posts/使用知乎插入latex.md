---
title: 使用知乎插入latex
date: 2021-09-06 20:00:51
tags: 
    - Python
    - hexo
toc: true
categories: 网页
---

在使用Hexo中，出现插入数学公式问题，后来在群里一位大佬帮助下，通过如下链接返回公式图片
<!--more-->
https://www.zhihu.com/equation?tex={latex公式}


在使用过程中发现，需要将参数转为符合url规则
python代码如下

```python
import requests

while True:
    Gongshi = input('输入要转换的公式：')
    value = requests.utils.quote(Gongshi)
    print(f"https://www.zhihu.com/equation?tex={value}")
```