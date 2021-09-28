---
title: 爬取LOL壁纸音频
date: 2020-04-12 19:07:03
tags:
    - Python
    - 爬虫
toc: true
categories: Python
---

因为之前有爬取王者荣耀的经验，所以爬取LOL时候全程无百度，令我惊奇的是，竟然有语音可以爬取
不过什么时候LOL变成腾讯的了？？？之前一直以为LOL是外国的。
因为不玩LOL，爬取了之后没看

```python
'''
    爬取LOL皮肤壁纸
    作者： ruxia-TJY
'''
import requests
import json
import os

# 英雄列表json文件链接
herolist = 'https://game.gtimg.cn/images/lol/act/img/js/heroList/hero_list.js'
# 获取
herolistjson = json.loads(requests.utils.unquote(requests.get(herolist).text))

for hero in herolistjson["hero"]:
    # 爬取语音
    if not os.path.exists('.\\data\\audio\\' + hero["title"]):
        os.mkdir('.\\data\\audio\\' + hero["title"])

    selectAudio = requests.get(hero['selectAudio'])
    with open('.\\data\\audio\\' + hero["title"] + '\\selectAudio.ogg','wb') as f1:
        f1.write(selectAudio.content)
        print( hero["title"] + '\\selectAudio.ogg is ok')

    banAudio = requests.get(hero['banAudio'])
    with open('.\\data\\audio\\' + hero["title"] + '\\banAudio.ogg','wb') as f2:
        f2.write(banAudio.content)
        print(hero["title"] + '\\banAudio.ogg is ok')

    # 爬取图片
    if not os.path.exists('.\\data\\img\\' + hero["title"]):
        os.mkdir('.\\data\\img\\' + hero["title"])
    
    imgjsonurl = 'https://game.gtimg.cn/images/lol/act/img/js/hero/' + hero['heroId'] + '.js'
    jsonr = json.loads(requests.utils.unquote(requests.get(imgjsonurl).text))

    for skin in jsonr["skins"]:
        try:
            img = requests.get(skin["mainImg"])
            with open('.\\data\\img\\' + hero["title"] + '\\' + skin["name"] + ".jpg" ,'wb') as f:
                f.write(img.content)
            print(hero["title"] + '\\' + skin["name"] + ".jpg is ok")
        except:pass
        else:pass
```