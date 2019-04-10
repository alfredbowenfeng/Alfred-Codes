---
title: Lianjia Crawler
date: 2018-10-01 18:15:19
tags: 
	- Python
categories: Data
---

# Introduction
The price of real estate in Shanghai has surged since the past years. This summer, I started developing a project with Jiuzhou, to get the data from [lianjia](https://sh.lianjia.com), as it is one of the most famous real estate brokers in China, and to conduct a preliminary analysis of the housing market in Shanghai. We successfully created a lianjia web cralwer, but the rest was stalled due to lack of time. 

In the City Economics and Management class next Friday, three students as representatives of our group will give a presentation about the housing price phenomenon in Shanghai. We certainly could take advantage of the crawler. To update the data gathered, I ran the crawler again but failed. It turned out the web engineers in lianjia had made some changes to the html pages. Therefore, I am going to re-create a crawler. 

<!-- more -->

# Page Analysis
Most property in Shanghai are apratments. There are three kinds of apartments: second-handed, new, and rental. The prices of new apartments are rigged by the government, and thus I decide to get the data of second-handed apartments. 

## List Page
List pages are like the following. 
![List Page Sample1](/images/lianjia-crawler/list-page1.png)

There are currently 66,422 second-handed apartments listed on lianjia in Shanghai, which means there are 66,422 individual pages for each of them. However, only 30 individual aprtments are listed on each page, and there are at most 100 pages, which means I can access at most 3,000 individual links from each basic list page. Therefore, I will apply filtering before crawling. 
![List Page Sample2](/images/lianjia-crawler/list-page2.png)

## Individual Page
Individual links are like the following, made up of three parts.
```
'https://sh.lianjia.com/ershoufang/' + id + '.html'
eg.
https://sh.lianjia.com/ershoufang/107100255027.html
```

Individual pages are like the following, showing tons of useful information.
![Individual Page Sample1](/images/lianjia-crawler/individual-page1.png)
![Individual Page Sample2](/images/lianjia-crawler/individual-page2.png)

# Web Crawler
I would like to create a web crawler made up of two parts: link crawler and data crawler. Link crawler gets all the links of each individual apartment page. Then, the data crawler gets individual apartment information through the links. 

## Link Crawler
As is mentioned before, only 30 individual aprtments are listed on each page, and there are at most 100 pages, which means I can access at most 3,000 individual links from each basic list page. Therefore, I will apply filtering before crawling. The filters are as follows.
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import requests, re, csv, codecs, time

pudong = ['https://sh.lianjia.com/ershoufang/pudong/lc1l1/', 'https://sh.lianjia.com/ershoufang/pudong/lc1l2/', 'https://sh.lianjia.com/ershoufang/pudong/lc1l3l4l5l6/', 'https://sh.lianjia.com/ershoufang/pudong/lc2l1l3l4l5l6/', 'https://sh.lianjia.com/ershoufang/pudong/bt1lc2l2/', 'https://sh.lianjia.com/ershoufang/pudong/bt2lc2l2/', 'https://sh.lianjia.com/ershoufang/pudong/lc3l1/', 'https://sh.lianjia.com/ershoufang/pudong/lc3l2a1a2/', 'https://sh.lianjia.com/ershoufang/pudong/lc3l2a3a4a5a6a7/', 'https://sh.lianjia.com/ershoufang/pudong/lc3l3l4l5l6/']
minhang = ['https://sh.lianjia.com/ershoufang/minhang/lc1/', 'https://sh.lianjia.com/ershoufang/minhang/lc2/', 'https://sh.lianjia.com/ershoufang/minhang/lc3l1l2/', 'https://sh.lianjia.com/ershoufang/minhang/lc3l3l4l5l6/']
baoshan = ['https://sh.lianjia.com/ershoufang/baoshan/lc1/', 'https://sh.lianjia.com/ershoufang/baoshan/lc2/', 'https://sh.lianjia.com/ershoufang/baoshan/lc3/']
xuhui = ['https://sh.lianjia.com/ershoufang/xuhui/lc1/', 'https://sh.lianjia.com/ershoufang/xuhui/lc2/', 'https://sh.lianjia.com/ershoufang/xuhui/lc3/']
putuo = ['https://sh.lianjia.com/ershoufang/putuo/lc1/', 'https://sh.lianjia.com/ershoufang/putuo/lc2/', 'https://sh.lianjia.com/ershoufang/putuo/lc3/']
yangpu = ['https://sh.lianjia.com/ershoufang/yangpu/lc1/', 'https://sh.lianjia.com/ershoufang/yangpu/lc2/', 'https://sh.lianjia.com/ershoufang/yangpu/lc3/']
changning = ['https://sh.lianjia.com/ershoufang/changning/lc1/', 'https://sh.lianjia.com/ershoufang/changning/lc2/', 'https://sh.lianjia.com/ershoufang/changning/lc3/']
songjiang = ['https://sh.lianjia.com/ershoufang/songjiang/lc1/', 'https://sh.lianjia.com/ershoufang/songjiang/lc2/', 'https://sh.lianjia.com/ershoufang/songjiang/lc3/']
jiading = ['https://sh.lianjia.com/ershoufang/jiading/lc1/', 'https://sh.lianjia.com/ershoufang/jiading/lc2/', 'https://sh.lianjia.com/ershoufang/jiading/lc3/']
huangpu = ['https://sh.lianjia.com/ershoufang/huangpu//']
jingan = ['https://sh.lianjia.com/ershoufang/jingan//']
zhabei = ['https://sh.lianjia.com/ershoufang/zhabei/lc1/', 'https://sh.lianjia.com/ershoufang/zhabei/lc2/', 'https://sh.lianjia.com/ershoufang/zhabei/lc3/']
hongkou = ['https://sh.lianjia.com/ershoufang/hongkou//']
qingpu = ['https://sh.lianjia.com/ershoufang/qingpu/lc1/', 'https://sh.lianjia.com/ershoufang/qingpu/lc2/', 'https://sh.lianjia.com/ershoufang/qingpu/lc3/']
fengxian = ['https://sh.lianjia.com/ershoufang/fengxian//']
jinshan = ['https://sh.lianjia.com/ershoufang/jinshan//']
chongming = ['https://sh.lianjia.com/ershoufang/chongming//']
shanghaizhoubian = ['https://sh.lianjia.com/ershoufang/shanghaizhoubian//']
districtNest = [pudong, minhang, baoshan, xuhui, putuo, yangpu, changning, songjiang, jiading, huangpu, jingan, zhabei, hongkou, qingpu, fengxian, jinshan, chongming, shanghaizhoubian]
```

I added `time.sleep()` function in case of web page request failure, which happens a lot. 
```
pglink, plink = [], []
district = []
for i in range(0, len(districtNest)):
    for j in range(0, len(districtNest[i])):
        district.append(districtNest[i][j])
print(len(district))

# get total page number
for i in range(0, len(district)):
    page1Text = s.get(district[i][:-1]).text
    totalPage = int(re.findall('{"totalPage":(.*?),', page1Text)[0])
    for j in range(1, totalPage + 1):
        pglink.append(district[i][:-1] + 'pg' + str(j) + '/')
for i in range(0, len(pglink)):
    try:
        page1Text = re.findall('><div class="item" data-houseid="(.*?)</div></div', s.get(pglink[i]).text)
    except:
        time.sleep(30)
        print('sleep for 30s ' + pglink[i][34:])
        page1Text = re.findall('><div class="item" data-houseid="(.*?)</div></div', s.get(pglink[i]).text)
    for j in range(0, len(page1Text)):
        plink.append(re.findall('<a class="title" href="(.*?)"', page1Text[j])[0])
    print(pglink[i][34:] + '         ', end='\r')
```

I output the result as a transition file `plink.csv`.
```
import csv
import csv
with open('C:\\Users\\Bowen Feng\\Projects\\sh-lianjia_data_analysis\\plink.csv', 'w', newline='') as csvfile:
    spamwriter = csv.writer(csvfile)
    for i in plink:
        spamwriter.writerow([i])
```

## Data Crawler
Through the Link Crawler, I get the individual page links of all the 66,422 apartments. Then I am able to acquire the information from the individual pages. 
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import requests, re, csv, codecs, time

s = requests.Session()
plink = []
with open('C:\\Users\\Bowen Feng\\Projects\\sh-lianjia_data_analysis\\plink.csv', newline='') as csvfile:
    spamreader = csv.reader(csvfile)
    for row in spamreader:
        for item in row:
            plink.append(item)
```

From the individual page, I acquire the information through regular expressions. Also, I used multiple safe measures to avoid errors caused by web page request failure by `try` and `except`. 
```
adata = [['链家网址', '标题', '价格', '每平米价格', '小区', '行政区', '街道', '地铁', '房屋户型', '所在楼层', '建筑面积', '户型结构', '套内面积', '建筑类型', '房屋朝向', '建筑结构', '装修情况', '梯户比例', '配备电梯', '产权年限', '挂牌时间', '交易权属', '上次交易', '房屋用途', '房屋年限', '产权所属', '房本备件', '其他1', '其他2', '其他3', '其他4', ]]
house = []
adata = []
for i in range(0, len(plink)):
    try:
        page2Text2 = s.get(plink[i]).text
    except:
        try:
            print('sleep for 30s ' + plink[i][34:])
            time.sleep(30)
            page2Text2 = s.get(plink[i]).text
        except:
            print('sleep for another 120s' + plink[i][34:])
            time.sleep(120)
            page2Text2 = s.get(plink[i]).text
    try:
        title = re.findall('title="(.*?)</h1>', page2Text2)[0]
        total = re.findall('class="total">(.*?)</span>', page2Text2)[0] + '万'
        unitPriceValue = re.findall('class="unitPriceValue">(.*?)<i>', page2Text2)[0] + '元/平米'
        neighbourhood = re.findall('class="info ">(.*?)</a>', page2Text2)[0]
        district = re.findall('target="_blank">(.*?)</', page2Text2)[2]
        place = re.findall('target="_blank">(.*?)</', page2Text2)[3]
        try:
            subway = re.findall(';">(.*?)</a>', page2Text2)[0]
        except:
            subway = ''
        try:
            label11 = re.findall('</span>(.*?)</li>\n', page2Text2)[0]
            label12 = re.findall('</span>(.*?)</li>\n', page2Text2)[1]
            label13 = re.findall('</span>(.*?)</li>\n', page2Text2)[2]
            label14 = re.findall('</span>(.*?)</li>\n', page2Text2)[3]
            label15 = re.findall('</span>(.*?)</li>\n', page2Text2)[4]
            label16 = re.findall('</span>(.*?)</li>\n', page2Text2)[5]
            label17 = re.findall('</span>(.*?)</li>\n', page2Text2)[6]
            label18 = re.findall('</span>(.*?)</li>\n', page2Text2)[7]
            label19 = re.findall('</span>(.*?)</li>\n', page2Text2)[8]
            label110 = re.findall('</span>(.*?)</li>\n', page2Text2)[9]
            label111 = re.findall('</span>(.*?)</li>\n', page2Text2)[10]
            label112 = re.findall('</span>(.*?)</li>\n', page2Text2)[11]
            label21 = re.findall('<span>(.*?)</span>\n', page2Text2)[0]
            label22 = re.findall('<span>(.*?)</span>\n', page2Text2)[1]
            label23 = re.findall('<span>(.*?)</span>\n', page2Text2)[2]
            label24 = re.findall('<span>(.*?)</span>\n', page2Text2)[3]
            label25 = re.findall('<span>(.*?)</span>\n', page2Text2)[4]
            label26 = re.findall('<span>(.*?)</span>\n', page2Text2)[5]
            label28 = re.findall('<span>(.*?)</span>\n', page2Text2)[6]
            try:
                tag1 = re.findall('>(.*?)<', re.findall('<a class="tag.*?</a>', page2Text2)[0])[0]
            except:
                tag1 = ''
            try:
                tag2 = re.findall('>(.*?)<', re.findall('<a class="tag.*?</a>', page2Text2)[1])[0]
            except:
                tag2 = ''
            try:
                tag3 = re.findall('>(.*?)<', re.findall('<a class="tag.*?</a>', page2Text2)[2])[0]
            except:
                tag3 = ''
            try:
                tag4 = re.findall('>(.*?)<', re.findall('<a class="tag.*?</a>', page2Text2)[3])[0]
            except:
                tag4 = ''
            idata = [plink[i], title, total, unitPriceValue, neighbourhood, district, place, subway, label11, label12, label13, label14, label15, label16, label17, label18, label19, label110, label111, label112, label21, label22, label23, label24, label25, label26, label28, tag1, tag2, tag3, tag4]
            adata.append(idata)
            print(str(i+1) + ' of ' + str(len(plink)) + '     ', end = '\r')
        except:
            house.append(plink[i])
            print('house ' + plink[i][34:])
            print(str(i+1) + ' of ' + str(len(plink)) + '     ', end = '\r')
    except:
        print('error ' + plink[i][34:])
        print(str(i+1) + ' of ' + str(len(plink)) + '     ', end = '\r')
```

Still, errors appear. It turns out that they are caused by house pages instead of apartment pages. Therefore, I list them as error and later get the house information through another crawler. 
```
house = [['链家网址', '标题', '价格', '每平米价格', '小区', '行政区', '街道', '地铁', '房屋户型', '所在楼层', '建筑面积', '套内面积', '房屋朝向', '建筑结构', '装修情况', '别墅类型', '产权年限', '挂牌时间', '交易权属', '上次交易', '房屋用途', '房屋年限', '产权所属', '房本备件', '其他1', '其他2', '其他3', '其他4', ]]
for i in range(0, len(hlist)):
    try:
        page2Text2 = s.get(hlist[i]).text
    except:
        try:
            print('sleep for 30s ' + hlist[i][34:])
            time.sleep(30)
            page2Text2 = s.get(hlist[i]).text
        except:
            print('sleep for another 120s' + hlist[i][34:])
            time.sleep(120)
            page2Text2 = s.get(hlist[i]).text
    try:
        title = re.findall('title="(.*?)</h1>', page2Text2)[0]
        total = re.findall('class="total">(.*?)</span>', page2Text2)[0] + '万'
        unitPriceValue = re.findall('class="unitPriceValue">(.*?)<i>', page2Text2)[0] + '元/平米'
        neighbourhood = re.findall('class="info ">(.*?)</a>', page2Text2)[0]
        district = re.findall('target="_blank">(.*?)</', page2Text2)[2]
        place = re.findall('target="_blank">(.*?)</', page2Text2)[3]
        try:
            subway = re.findall(';">(.*?)</a>', page2Text2)[0]
        except:
            subway = ''
        try:
            label11 = re.findall('</span>(.*?)</li>\n', page2Text2)[0]
            label12 = re.findall('</span>(.*?)</li>\n', page2Text2)[1]
            label13 = re.findall('</span>(.*?)</li>\n', page2Text2)[2]
            label14 = re.findall('</span>(.*?)</li>\n', page2Text2)[3]
            label15 = re.findall('</span>(.*?)</li>\n', page2Text2)[4]
            label16 = re.findall('</span>(.*?)</li>\n', page2Text2)[5]
            label17 = re.findall('</span>(.*?)</li>\n', page2Text2)[6]
            label18 = re.findall('</span>(.*?)</li>\n', page2Text2)[7]
            label19 = re.findall('</span>(.*?)</li>\n', page2Text2)[8]
            label21 = re.findall('<span>(.*?)</span>\n', page2Text2)[0]
            label22 = re.findall('<span>(.*?)</span>\n', page2Text2)[1]
            label23 = re.findall('<span>(.*?)</span>\n', page2Text2)[2]
            label24 = re.findall('<span>(.*?)</span>\n', page2Text2)[3]
            label25 = re.findall('<span>(.*?)</span>\n', page2Text2)[4]
            label26 = re.findall('<span>(.*?)</span>\n', page2Text2)[5]
            label28 = re.findall('<span>(.*?)</span>\n', page2Text2)[6]
            try:
                tag1 = re.findall('>(.*?)<', re.findall('<a class="tag.*?</a>', page2Text2)[0])[0]
            except:
                tag1 = ''
            try:
                tag2 = re.findall('>(.*?)<', re.findall('<a class="tag.*?</a>', page2Text2)[1])[0]
            except:
                tag2 = ''
            try:
                tag3 = re.findall('>(.*?)<', re.findall('<a class="tag.*?</a>', page2Text2)[2])[0]
            except:
                tag3 = ''
            try:
                tag4 = re.findall('>(.*?)<', re.findall('<a class="tag.*?</a>', page2Text2)[3])[0]
            except:
                tag4 = ''
            ihouse = [hlist[i], title, total, unitPriceValue, neighbourhood, district, place, subway, label11, label12, label13, label14, label15, label16, label17, label18, label19, label21, label22, label23, label24, label25, label26, label28, tag1, tag2, tag3, tag4]
            house.append(ihouse)
            print(str(i+1) + ' of ' + str(len(hlist)) + '     ', end = '\r')
        except:
            house.append(hlist[i])
            print('house ' + hlist[i][34:])
            print(str(i+1) + ' of ' + str(len(hlist)) + '     ', end = '\r')
    except:
        print('error ' + hlist[i][34:])
        print(str(i+1) + ' of ' + str(len(hlist)) + '     ', end = '\r')
```

Finally, I output both files containing apartment and house information respectively. 
```
f = codecs.open('C:\\Users\\Bowen Feng\\Projects\sh-lianjia_data_analysis\\apartment.csv', 'w', 'utf_8_sig')
spamwriter = csv.writer(f)
for row in adata:
    spamwriter.writerow(row)

f = codecs.open('C:\\Users\\Bowen Feng\\Projects\sh-lianjia_data_analysis\\house.csv', 'w', 'utf_8_sig')
spamwriter = csv.writer(f)
for row in house:
    spamwriter.writerow(row)
```

I will talk about my approach to the proliminary analysis in the next post. 