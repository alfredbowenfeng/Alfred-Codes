---
title: Lianjia Preliminary Analysis
date: 2018-10-02 10:11:38
tags:
	- Python
categories: Data
---

# Introduction
In the [previous post](https://bowenf.com/20181001/lianjia-crawler/), I have made a web crawler for the information of seconded-handed apartments and houses on [lianjia](https://sh.lianjia.com). Now I am going to conduct a preliminary analysis. 

Let's take a look at the data acquired in Excel. 
![Present from Excel](/images/lianjia-preliminary-analysis/data-excel.png)

<!-- more -->

# Present Situation Analysis
After applying some simple pre-processing methods in Excel, I open the file in Jupyter Notebook, and make some changes about the variables. 
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error
from sklearn import linear_model

train = pd.read_csv('C:\\Users\\Bowen Feng\\Projects\\sh-lianjia_data_analysis\\apartment-prep.csv')

train['EAST'] = train['EAST'].apply(str)
train['SOUTH'] = train['SOUTH'].apply(str)
train['WEST'] = train['WEST'].apply(str)
train['NORTH'] = train['NORTH'].apply(str)
train['TMREG'] = train['TMREG'].apply(str)
train['TMLAST'] = train['TMLAST'].apply(str)
train['ANYTIME'] = train['ANYTIME'].apply(str)

train.drop('ID', axis = 1, inplace = True)
train.drop("TITLE", axis = 1, inplace = True)
train.drop("IAREA", axis = 1, inplace = True)
```

Then, I conduct a series of analysis concerning the relationship between Median Price per Square Meter and other variables. 

```
metro_order = ['1号线', '2号线', '3号线', '4号线', '5号线', '6号线', '7号线', '8号线', '9号线', '10号线', '11号线', '12号线', '13号线', '16号线', '17号线', 'NA']
condition_pivot = train.pivot_table(index='METRO', values='PPS', aggfunc=np.median)
condition_pivot.loc[metro_order].plot(kind='bar')
plt.xlabel('地铁线路')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Metro](/images/lianjia-preliminary-analysis/metro.png)

```
floor_order = ['低楼层', '中楼层', '高楼层']
condition_pivot = train.pivot_table(index='FLR', values='PPS', aggfunc=np.median)
condition_pivot.loc[floor_order].plot(kind='bar')
plt.xlabel('所在楼层')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Floor](/images/lianjia-preliminary-analysis/floor.png)

```
plt.scatter(x=train['TFLR'], y=train['PPS'])
plt.ylabel('Scatter Price per Square Meter')
plt.xlabel('总楼层')
```
![Total Floor](/images/lianjia-preliminary-analysis/total-floor.png)

```
vstr_order = ['平层', '复式', '跃层', '错层', 'NA']
condition_pivot = train.pivot_table(index='VSTR', values='PPS', aggfunc=np.median)
condition_pivot.loc[vstr_order].plot(kind='bar')
plt.xlabel('户型结构')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Vertical Structure](/images/lianjia-preliminary-analysis/vertical-structure.png)

```
type_order = ['板楼', '塔楼', '板塔结合', '平房', 'NA']
condition_pivot = train.pivot_table(index='TYPE', values='PPS', aggfunc=np.median)
condition_pivot.loc[type_order].plot(kind='bar')
plt.xlabel('建筑类型')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Type](/images/lianjia-preliminary-analysis/type.png)

```
str_order = ['钢混结构', '砖混结构', 'NA']
condition_pivot = train.pivot_table(index='STR', values='PPS', aggfunc=np.median)
condition_pivot.loc[str_order].plot(kind='bar')
plt.xlabel('建筑结构')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Building Structure](/images/lianjia-preliminary-analysis/building-structure.png)

```
dec_order = ['简装', '精装', '毛坯', '其他']
condition_pivot = train.pivot_table(index='DEC', values='PPS', aggfunc=np.median)
condition_pivot.loc[dec_order].plot(kind='bar')
plt.xlabel('装修情况')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Decoration Status](/images/lianjia-preliminary-analysis/decoration-status.png)

```
plt.scatter(x=train['RTO'], y=train['PPS'])
plt.ylabel('Scatter Price per Square Meter')
plt.xlabel('梯户比例')
```
![Ratio](/images/lianjia-preliminary-analysis/ratio.png)

```
wlft_order = ['有', '无', 'NA']
condition_pivot = train.pivot_table(index='WLFT', values='PPS', aggfunc=np.median)
condition_pivot.loc[wlft_order].plot(kind='bar')
plt.xlabel('是否有电梯')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Lifter](/images/lianjia-preliminary-analysis/lifter.png)

```
yright_order = ['40年', '50年', '70年', 'NA']
condition_pivot = train.pivot_table(index='YRIGHT', values='PPS', aggfunc=np.median)
condition_pivot.loc[yright_order].plot(kind='bar')
plt.xlabel('产权年限')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Years of Property Right](/images/lianjia-preliminary-analysis/years-property-right.png)

```
dright_order = ['商品房', '动迁安置房', '售后公房']
condition_pivot = train.pivot_table(index='DRIGHT', values='PPS', aggfunc=np.median)
condition_pivot.loc[dright_order].plot(kind='bar')
plt.xlabel('交易权属')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Trading Right](/images/lianjia-preliminary-analysis/trading-right.png)

```
use_order = ['普通住宅', '商业办公类', '新式里弄', '旧式里弄', '花园洋房', '老公寓']
condition_pivot = train.pivot_table(index='USE', values='PPS', aggfunc=np.median)
condition_pivot.loc[use_order].plot(kind='bar')
plt.xlabel('房屋用途')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Use](/images/lianjia-preliminary-analysis/use.png)

```
ycondo_order = ['未满两年', '满两年', '满五年', 'NA']
condition_pivot = train.pivot_table(index='YCONDO', values='PPS', aggfunc=np.median)
condition_pivot.loc[ycondo_order].plot(kind='bar')
plt.xlabel('房屋年限')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Tax](/images/lianjia-preliminary-analysis/tax.png)

```
cright_order = ['共有', '非共有', 'NA']
condition_pivot = train.pivot_table(index='CRIGHT', values='PPS', aggfunc=np.median)
condition_pivot.loc[cright_order].plot(kind='bar')
plt.xlabel('产权所属')
plt.ylabel('Median Price per Square Meter')
plt.xticks(rotation=0)
```
![Claim Right](/images/lianjia-preliminary-analysis/claim-right.png)

# Future Prediction
According to the textbook, we can predict the future housing prices by Price-to-Rent Ratio. 

> The price-to-rent ratio is the ratio of home prices to annualized rent in a given location, and is used as a benchmark for estimating whether it is cheaper to rent or own a property. - [Investopedia](https://www.investopedia.com/terms/p/price-to-rent-ratio.asp)

The benchmark price-to-rent ratio should be 1%-2% above the home loan interest rate. The home loan interest rate is around 5% in China, and thus the benchmark price-to rent ratio should be around 6%-7%. If the ratio is above 7%, it is cheaper to buy an apartment, and the housing price tends to rise in the future. If the ratio is below 6%, it is cheaper to rent an apartment, and the housing price tends to fall in the future. 

To get the price-to-rent ratio in Shanghai, I also created a web crawler for the rental apartments on [lianjia](https://sh.lianjia.com), in order to calculate the median price-to-rent ratio. 
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import requests, re, csv, codecs, time
s = requests.Session()

pudong = ['https://sh.lianjia.com/zufang/pudong/ra1ra2ra3/', 'https://sh.lianjia.com/zufang/pudong/ra4ra5/', 'https://sh.lianjia.com/zufang/pudong/ra6/']
minhang = ['https://sh.lianjia.com/zufang/minhang//']
baoshan = ['https://sh.lianjia.com/zufang/baoshan//']
xuhui = ['https://sh.lianjia.com/zufang/xuhui//']
putuo = ['https://sh.lianjia.com/zufang/putuo//']
yangpu = ['https://sh.lianjia.com/zufang/yangpu//']
changning = ['https://sh.lianjia.com/zufang/changning//']
songjiang = ['https://sh.lianjia.com/zufang/songjiang//']
jiading = ['https://sh.lianjia.com/zufang/jiading//']
huangpu = ['https://sh.lianjia.com/zufang/huangpu//']
jingan = ['https://sh.lianjia.com/zufang/jingan//']
zhabei = ['https://sh.lianjia.com/zufang/zhabei//']
hongkou = ['https://sh.lianjia.com/zufang/hongkou//']
qingpu = ['https://sh.lianjia.com/zufang/qingpu//']
fengxian = ['https://sh.lianjia.com/zufang/fengxian//']
jinshan = ['https://sh.lianjia.com/zufang/jinshan//']
chongming = ['https://sh.lianjia.com/zufang/chongming//']
districtNest = [pudong, minhang, baoshan, xuhui, putuo, yangpu, changning, songjiang, jiading, huangpu, jingan, zhabei, hongkou, qingpu, fengxian, jinshan, chongming]
district = []
for i in range(0, len(districtNest)):
    for j in range(0, len(districtNest[i])):
        district.append(districtNest[i][j])

pglink, plink = [], []
for i in range(0, len(district)):
    page1Text = s.get(district[i][:-1]).text
    totalPage = int(re.findall('{"totalPage":(.*?),', page1Text)[0])
    for j in range(1, totalPage + 1):
        pglink.append(district[i][:-1] + 'pg' + str(j) + '/')

for i in range(0, len(pglink)):
    try:
        page1Text = s.get(pglink[i]).text
        listLinks = re.findall('<h2><a target="_blank" href="(.*?)" title', page1Text)
    except:
        time.sleep(30)
        print('sleep for 30s ' + pglink[i][30:])
        page1Text = s.get(pglink[i]).text
        listLinks = re.findall('<h2><a target="_blank" href="(.*?)" title', page1Text)
    for j in range(0, len(listLinks)):
        plink.append(listLinks[j])
    print(pglink[i][30:] + '         ', end='\r')

import csv
with open('C:\\Users\\Bowen Feng\\Projects\\sh-lianjia_data_analysis\\rental-plink.csv', 'w', newline='') as csvfile:
    spamwriter = csv.writer(csvfile)
    for i in plink:
        spamwriter.writerow([i])

adata = [['链家网址', '标题', '每月价格', '面积', '房屋户型', '楼层', '房屋朝向', '地铁', '小区', '行政区', '街道', '时间']]
error = []
for i in range(0, len(plink)):
    try:
        page2Text2 = s.get(plink[i]).text
    except:
        try:
            print('sleep for 30s ' + plink[i][30:])
            time.sleep(30)
            page2Text2 = s.get(plink[i]).text
        except:
            print('sleep for another 120s' + plink[i][30:])
            time.sleep(120)
            page2Text2 = s.get(plink[i]).text
    try:
        title = re.findall('<h1 class="main">(.*?)</h1>', page2Text2)[0]
        permonth = re.findall('<span class="total">(.*?)</span>', page2Text2)[0]
        area = re.findall('<i>面积：</i>(.*?)</p>', page2Text2)[0]
        strc = re.findall('<i>房屋户型：</i>(.*?)</p>', page2Text2)[0]
        flr = re.findall('<i>楼层：</i>(.*?)</p>', page2Text2)[0]
        dirc = re.findall('<i>房屋朝向：</i>(.*?)</p>', page2Text2)[0]
        subw = re.findall('<i>地铁：</i>(.*?)</p>', page2Text2)[0]
        nei = re.findall('">(.*?)</a>', re.findall('<i>小区：</i>.*?</a>', page2Text2)[0])[0]
        loc = re.findall('<p><i>位置：</i>.*?</a></p>', page2Text2)[0]
        dis = re.findall('">(.*?)</a>', loc)[0]
        plc = re.findall('">(.*?)</a>', loc)[1]
        tm = re.findall('<i>时间：</i>(.*?)</p>', page2Text2)[0]
        idata = [plink[i], title, permonth, area, strc, flr, dirc, subw, nei, dis, plc, tm]
        adata.append(idata)
        print(str(i+1) + ' of ' + str(len(plink)) + '     ', end = '\r')
    except:
        print('error ' + plink[i][30:])
        error.append(plink[i])
        print(str(i+1) + ' of ' + str(len(plink)) + '     ', end = '\r')

f = codecs.open('C:\\Users\\Bowen Feng\\Projects\\sh-lianjia_data_analysis\\rental-apartment.csv', 'w', 'utf_8_sig')
spamwriter = csv.writer(f)
for row in adata:
    spamwriter.writerow(row)
```

The price-to-rent ratios of each district are shown as follows.
![Price-to-Rent Ratio](/images/lianjia-preliminary-analysis/price-to-rent-ratio.png)

The price-to-rent ratio concerning the housing market in Shanghai is around 2%, far below the benchmark 6%-7%. According to the textbook theory, the housing price tends to fall in the future. However, if we take other situations into consideration, it is absolutely not the case, and the housing price is highly unlikely to fall in the future. The detailed analysis and reasons will be given in the future posts. 