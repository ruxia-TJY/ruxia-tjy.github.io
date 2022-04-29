---
title: tColorP-python控制台彩色输出
date: 2022-04-29 10:54:19
tags:
    - Python
    - 我的开源
toc: true
categories: 我的开源
---

{% inlineImg https://img.shields.io/badge/language-Python-blue%}
{% inlineImg https://img.shields.io/github/languages/code-size/ruxia-TJY/TColorP %}
{% inlineImg https://img.shields.io/badge/-Linux-yellow?logo=linux %}
{% inlineImg https://img.shields.io/badge/-Linux-yellow?logo=windows %}
{% inlineImg https://img.shields.io/pypi/l/tColorP %}
{% inlineImg https://img.shields.io/pypi/v/tColorP?logo=pypi&logoColor=ffffff&label=PyPI&labelColor=blue %}



通过在输出文件开头和结尾添加`\033[输出模式;文字颜色;背景色`进行改变


## 使用方法

您可以使用
```cmd
pip install tColorP
```
安装

```python
from TColorP import TColorP, TColor

tcp = TColorP()

# print success info 
tcp.success('success')
# print error info
tcp.error('error')
# print warning info
tcp.warning('warning')
# print normal info
tcp.normal('normal')

# print normal Style
print(tcp.normalStyle)

# change normal style
tcp.normalStyle = [TColor.method_highlight,TColor.foreground_red]
tcp.normal("normal")

# change normal style once
tcp.normal('normal once',style=[TColor.method_highlight,TColor.foreground_red])

# format color
tcp.formatColor([TColor.method_normal,TColor.foreground_red])
print('this will change color with red')

# print suppos method and color
print(TColor())
# method_normal=0,method_highlight=1,method_underline=4,method_reverse=7
# foreground_black=30,foreground_red=31,foreground_green=32,foreground_yellow=33,foreground_blue=34,foreground_fuchsia=35,foreground_cyan=36,foreground_white=37
# background_black=40,background_red=41,background_green=42,background_yellow=43,background_blue=44,background_fuchsia=45,background_cyan=46,background_white=47
```

## 源码讲解

程序分为两个Class。

TColor存储支持的颜色和模式。

TColorP进行输出。

将要改变的样式作为列表传入formatColor函数中
```python
def formatColor(self,style:list) -> None:
        '''
            format color,print after this function will changed as arg style.
        :param style: list of style,use TColor or number
        :return: None
        '''
        if not len(style):
            return

        method = -1
        foreground = -1
        background = -1

        for i in style:
            if i in [0,1,4,7]:
                method = i
            if i >=30 and i <= 37:
                foreground = i
            if i >= 40 and i <= 47:
                background = i

        if method == -1:
            method = 0

        if background >= 0:
            print(f'\033[{method};{foreground};{background}m',end='')
        else:
            if foreground >= 0:
                print(f'\033[{method};{foreground}m',end='')
            else:
                print(f'\033[{method}m',end='')
```

例如默认的成功样式为正常输出，绿色文字
```python
def success(self,txt:str,**args) -> None:
        '''
        print any text with normal style and foreground is green,change once with style arg.
        :param txt: text you want to print
        :param args: end arg like print(),style arg for change print style once
        :return: None
        '''
        style = args.get('style',[TColor.method_normal,TColor.foreground_green])
        self.formatColor(style)
        end = args.get('end', '\n')
        print(txt, end=end)
        self.formatColor([TColor.method_normal])
```