---
title: 爬取bing每日壁纸
date: 2020-03-20 16:48:24
tags:
    - Python
    - 爬虫
toc: true
categories: Python
---


<div style="position: relative; padding: 30% 45%;">
<iframe style="position: absolute; width: 100%; height: 100%; left: 0; top: 0;" src="//player.bilibili.com/player.html?bvid=BV15E411N72T&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
</div>

必应壁纸的API:https://cn.bing.com/HPImageArchive.aspx
参数：
+ format(可选)
    如果不存在format参数则返回xml格式
    - js 返回json格式
    - xml   返回xml格式
  
+ idx()
    截止天数
    - 0 今天
    - 1 截止至明天
    - -1    截止至昨天

+ n
    返回的数量
    1-8

+ mkt
    地区
    zh-CN   中国

```python
"""
Python爬取bing每日壁纸
作者： ruxia-TJY
转载请注明：https://ruxia-tjy.github.io/
"""
import requests
import json
import time

def main():
    # bing壁纸API
    url = 'https://cn.bing.com/HPImageArchive.aspx?format=js&idx=0&n=1&mkt=zh-CN'
    # 获取json
    jsonr  requests.get(url).text
    # 获取图片路径
    imgpath = json.loads(jsonr)["images"][0]["url"]
    # 拼接路径
    imgurl = 'http://cn.bing.com' + imgpath
    # 获取壁纸
    img = requests.get(imgurl)
    #保存名称
    savename = time.strftime("%Y-%m-%d") + '.jpg'
    # 保存图片
    with open(savename,'wb') as f1:
        f1.write(img.content)

if __name__ == '__main__':
    main()
```
