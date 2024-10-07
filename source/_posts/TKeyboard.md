---
title: TKeyboard
date: 2021-06-10 10:21:04
updated: 2022-04-13 22:28:50
cover: https://cdn.jsdelivr.net/gh/ruxia-TJY/BlogUse@latest/img/opensource/tkeyboard/TKeyboard.jpg
description: TKeyboard是基于AtMega32U4的小键盘项目，由于采用了Arduino开发，十分简单
tags: 
    - C/C++
    - DIY
    - 我的开源
    - 硬件
    - Arduino
toc: true
categories: 我的开源
---

## 描述

暂时使用Pro Micro(Mini接口)开发板，等到芯片的价格降下去，直接使用32U4贴上去缩小体积。

共计两个版本（两个玩具），具体内容详见相应文件夹的文档。

## Tkeyboard

{% image https://cdn.jsdelivr.net/gh/ruxia-TJY/BlogUse@latest/img/opensource/tkeyboard/TKeyboard.png, width=200px, alt=预览图。 %}

共计两块电路板，其中所有的电路都在主电路板上。

floor仅仅作为支撑外壳使用，上面没有任何电路，可以不打，将两块电路板用4个M3螺丝组装。

| 模块                    | 数量 |
| :-----------------------: | :----: |
| Leonardo Pro Micro      | 1    |
| 旋转编码器EC11    | 1    |
| 0.96寸OLED（可选）          | 1    |
| 按键（组成3x4矩阵键盘） | 12   |



| Leonardo引脚 |      元件       |
| :----------: | :-------------: |
|      2       |    OLED SDA     |
|      3       |    OLED SCL     |
|      5       |  LED 音量/亮度  |
|      6       | LED 音乐/浏览器 |
|      7       |    EC11 CLK     |
|      8       |     EC11 SW     |
|      9       |     EC11 DT     |
|      10      |   3x4矩阵 1列   |
|      16      |   3x4矩阵 2列   |
|      14      |   3x4矩阵 3列   |
|      15      |   3x4矩阵 4列   |
|      20      |   3x4矩阵 1行   |
|      19      |   3x4矩阵 2行   |
|      18      |   3x4矩阵 3行   |

### 焊接教程

{% gallery true,220,10 %}
![电路板正面图](https://cdn.jsdelivr.net/gh/ruxia-TJY/BlogUse@latest/img/opensource/tkeyboard/电路板_PreView.png)
![背面图](https://cdn.jsdelivr.net/gh/ruxia-TJY/BlogUse@latest/img/opensource/tkeyboard/电路板背面_PreView.png)
{% endgallery %}

1. 焊接好正面贴片元件，如上图显示焊接电阻，+为LED正极。
2. 两排1x4排母焊接在正面，供OLED插入，根据自己的OLED引脚选择焊接，有的OLED最左为VCC，有的为GND，注意区分。
3. 在电路板背面焊接1x12螺母，共Leonardo插入，根据自己购买的大小选择焊接两个即可。
4. 使用M3螺丝将电路板与底板连接起来

### 使用

旋钮存在两种模式，音量调节/亮度调节，按下 切换模式 按钮切换。

+ 处于音量调节模式时，音量/亮度指示灯亮起，左旋减小音量，右旋增加音量，按下切换静音
+ 处于亮度调节模式时，音量/亮度指示灯熄灭，左旋降低亮度，右旋增加亮度，按下切换静音

按键功能为标识所示

第二行按钮有两种模式，音乐/浏览器，按下 音乐/浏览器 按钮切换

+ 处于音乐模式时，音乐/浏览器指示灯亮起，按下按键执行 / 左边所示功能
+ 处于浏览器模式时。音乐/浏览器指示灯熄灭，按下按键执行 / 右边所示功能

### 修改

+ Pro Micro有两种，两种的电路板大小不同，排针位置不同。可自行选择删除排母
+ OLED有左边VCC和左边GND的，自行选择保留。
+ 当您对本项目电路图进行修改时，请删去作者图标：涂，以及作者:ruxia,并修改代码中将图标作者显示在OLED中的部分

## Tkeyboard2 （开发中）


{% image https://cdn.jsdelivr.net/gh/ruxia-TJY/BlogUse@latest/img/opensource/tkeyboard/TKeyboard2.png, width=200px, alt=TKeyboard预览图 %}

按下按键，从EEPROM相应地址读取操作字符串。对于媒体音量增加等使用转义字符串。例如提升音量存储为\m。

对于具体转义字符串请查看表。

原本制作了OLED动画，但是会导致旋转旋钮出现问题，所以注释掉。

8个LED显示灯通过74LS595控制，其中三个LED用于系统CAPS，NUMS，ScrollLock.剩余5个灯暂时未想好功能。

|   元件   |  封装    | 数量 |
| :----: | :----: | :----: |
| Leonardo Pro Micro | Mini接口 | 1 |
| 旋转编码器EC11 |  | 2 |
| 0.96寸OLED |  | 1 |
| 按键 |  | 25 |
| BC6301 | SOP-14 | 1 |
| AT24C04 | SOP-8 | 1 |
| 74LS595 | SOP-16 | 1 |
| LED | 0805 | 8 |
| 排母 | 1x12 | 2 |
| 排母 | 1x4 | 1 |
| 1N4148WS | SOD-323 | 25 |

### 修改键值

目前写死在程序里面，可以通过程序里面的setKeyForEEPROM()函数修改每个键值的功能，并调用一次该函数即可写入EEPROM。后续写一个软件，用以修改。

### 焊接教程
元件焊接位置见bom文件夹下文件



外壳有两种方案，一是像TKeyboard版本一样，由两块PCB板4枚M3螺丝组装。

二是3D打印（设计中），本人设计很烂，建议可以自行设计



## 开源链接
采用BSD3.0开源

{% btn 'https://gitee.com/ruxia-tjy/tkeyboard',Gitee链接,far fa-hand-point-right,blue larger %} {% btn 'https://github.com/ruxia-TJY/TKeyboard',Github链接,far fa-hand-point-right,blue larger %}
