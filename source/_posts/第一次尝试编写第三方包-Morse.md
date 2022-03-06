---
title: 第一次尝试编写第三方包-Morse
date: 2022-02-26 19:40:08
tags: 
  - 我的开源
  - Python
toc: true
categories: Python
---

{% inlineImg https://img.shields.io/badge/language-Python-blue%}
{% inlineImg https://img.shields.io/github/languages/code-size/ruxia-TJY/Morse %}
{% inlineImg https://img.shields.io/badge/-Linux-yellow?logo=linux %}
{% inlineImg https://img.shields.io/badge/-Linux-yellow?logo=windows %}
{% inlineImg https://img.shields.io/pypi/l/morse-ruxia-TJY %}
{% inlineImg https://img.shields.io/pypi/v/Morse-ruxia-TJY?logo=pypi&logoColor=ffffff&label=PyPI&labelColor=blue %}


第一次写第三方包，拿摩斯编解码练练手,BSD3协议开源在Github，已上传至PyPi.

## 下载

命令行中
```cmd
pip install Morse-ruxia-TJY
```

## 使用

```python
from Morse import morse

M = morse.Morse()

print(M.encode('morse'))
# ————/——————/.——./.../.

print(M.decode('————/——————/.——./.../.'))
# MORSE
M.isLower = True
print(M.decode('————/——————/.——./.../.'))
# morse

M.Separator = '?'
print(M.encode('morse'))
# ————?——————?.——.?...?.

M.dot = '<'
M.dash = '>'
print(M.encode('morse'))
# >>/>>>/<></<<</<
```