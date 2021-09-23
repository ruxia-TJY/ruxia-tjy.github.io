---
title: pyecharts制作新中国建交国家时间图
date: 2021-09-23 23:32:24
tags:
    - 中国
    - python
toc: true
categories: python
---

## 简介
数据来自政府[中华人民共和国与各国建立外交关系日期简表](http://www.gov.cn/guoqing/2017-06/14/content_5202420.htm "中华人民共和国与各国建立外交关系日期简表")

## 页面
点击 [新中国建交国家图](https://ruxia-tjy.github.io/map/新中国建交国家图.html "新中国建交国家图") 查看

## 代码

```python
import pandas as pd
from pyecharts import options as opts
from pyecharts.charts import Map

# 此文件见附件
data = pd.read_excel('data.xlsx',index_col=0)

c = (
    Map(init_opts=opts.InitOpts(width="1400px",height='600px'))
    
    .add("国家", [list(z) for z in zip(data['国家_re'], data['年份'])], "world",is_map_symbol_show=False)

    .set_series_opts(label_opts=opts.LabelOpts(is_show=True))
    .set_global_opts(
        title_opts=opts.TitleOpts(title="新中国建交国家时间",subtitle='马尔代夫-1972.10.14\n摩纳哥-1995.1.16\n圣马力诺1971.5.6\n多米尼克2004.3.23\n库克群岛1997.7.25\n技术原因未标注位置'),
        visualmap_opts=opts.VisualMapOpts(min_=1949,max_=2022),
    )
)
c.render("与新中国建交国家.html")       # 生成html文件
```

{% btn 'https://gitee.com/ruxia-tjy/bloguse/raw/master/file/2021/9/23/data.xlsx',点击下载附件,far fa-hand-point-right,outline %}

