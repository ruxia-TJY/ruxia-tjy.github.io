---
title: python登录校园网
date: 2021-10-11 20:00:26
tags:
    - Python
    - 爬虫
toc: true
categories: Python
---

校园网的登录可以通过requests一个url登录。

首先打开校园网登录界面，打开浏览器开发者工具，一般按F12就可以了。

输入完自己的密码，完成登录。在开发者工具中找到带有login的链接，复制请求URL和请求标头。

将请求url复制到下面代码的url中，请求标头替换下面的headers。

```python
import requests

url = ''
headers = {
    'Accept': '*/*',
    'Accept-Encoding': 'gzip, deflate',
    'Accept-Language': 'zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6',
    'Connection': 'keep-alive',
    'Host': '10.64.11.250:801',
    'Referer': 'http://10.64.11.250/',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36 Edg/94.0.992.38'
}
r = requests.get(url,headers=headers)
```

运行一下测试能否连接。

可以通过pyinstaller打包成可执行文件，添加到系统开机启动项中，就可以每次打开电脑自动设置连接校园网了