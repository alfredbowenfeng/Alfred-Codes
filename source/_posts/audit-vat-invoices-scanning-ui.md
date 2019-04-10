---
title: VAT Invoices Scanning and UI
date: 2018-08-08 02:03:34
tags:
	- Audit
	- Python
categories: Data
---

# Introduction
At EY, vouching as well as scanning invoices was one of the jobs that interns usually did. Nomrally, the invoices were for Value Added Tax (VAT). Audit interns manually recorded the information about dozens of or hundreds of invoices into Excel, which was sure to take tons of energy and time. 

<!-- more -->

Take this invoice, one of my personal ones, for instance. 
![Demo Invoice](/images/audit-vat-invoices-scanning-ui/IMG-0852.JPG)

The information that can be recorded is as the following. 
{% table %}
| Object | Information |
| --- | --- |
| Type | 增值税普通发票 |
| Code | 3100172320 |
| Number | 65485582 |
| Amount | 94.34 |
| Date | 2017/12/14 |
{% endtable %}

My originial thought was to train a model with [TensorFlow](https://www.tensorflow.org/), which required a large number of invoices when training. However, this morning I accidentally noticed the QR code on the upper-left corner of the invoice. Then I scanned it with my iPhone, and found that the information could be extracted through the QR code. 

# Package Testing
At first, I used `Pillow` and `pyzbar` to open the image and recognize the QR code respectively. I referred to [this article](https://pypi.org/project/pyzbar/) for reference. 
```
from pyzbar import pyzbar
from PIL import Image

def Decode(Image):
	decodedObjects = pyzbar.decode(Image)
	for obj in decodedObjects:
        print('Name: ', fList[i])
        print('Type: ', obj.type)
        print('Data: ', obj.data,'\n')

img = Image.open('/home/bowenfeng/Projects/audit-kit/sources/invoice-images/IMG-0852.JPG')
Decode(img)
```

Sadly, it returned nothing. I thought maybe it was because `pyzbar` could not locate the QR code, so I took a closer and clearer photo. 
![Closer Demo Invoice](/images/audit-vat-invoices-scanning-ui/IMG-0853.JPG)

Still, nothing. 

# Pre-Processing

## Function Definition
After some testing of photos pre-processed by myself through Photoshop, I found that the size and location of the QR code did not matter. The color and contrast mattered a lot. Therefore, I defined another function for pre-processing the photos. 
```
def Preproc():
	global img, img_b
	img_gray = img.convert('L')
	table, threshold = [], 150
	for p in range(256):
		if p < threshold:
			table.append(0)
		else:
			table.append(1)
	img_b = img_gray.point(table, '1')

img = Image.open('/home/bowenfeng/Projects/audit-kit/sources/invoice-images/IMG-0852.JPG')
Preproc()
Decode(Image)
```

The photo `img_gray` after the first pre-processing step was as the following. 
![img](/images/audit-vat-invoices-scanning-ui/preproc-img-gray.png)

The photo `img_b` after the second pre-processing step was as the following. 
![img](/images/audit-vat-invoices-scanning-ui/preproc-img-b.png)

It worked! Three lines of information were printed. 
```
Name:  IMG-0852.JPG
Type:  QRCODE
Data:  b'01,04,3100172320,65485582,94.34,20171214,67149474231194371469,D0C1,'
```

## Different Thresholds for Images with Different Brightness
The program with only one fixed threshold might work properly all the time. Therefore I set four thresholds in the pre-processing, so that it could deal with photos with different brightness.  
```
th1, th2, th3, th4 = 100, 125, 150, 175
def Preproc():
	global img, img_b
	img_gray = img.convert('L')
	try:
        table = []
        for p in range(256):
            if p < th1:
                table.append(0)
            else:
                table.append(1)
        img_b = img_gray.point(table, '1')
        Decode(img_b)
    except:
        try:
            table = []
            for p in range(256):
                if p < th2:
                   table.append(0)
                else:
                    table.append(1)
            img_b = img_gray.point(table, '1')
            Decode(img_b)
        except:
            try:
                table = []
                for p in range(256):
                    if p < th3:
                        table.append(0)
                    else:
                        table.append(1)
                img_b = img_gray.point(table, '1')
                Decode(img_b)
            except:
                try:
                    table = []
                    for p in range(256):
                        if p < th4:
                            table.append(0)
                        else:
                            table.append(1)
                    img_b = img_gray.point(table, '1')
                    Decode(img_b)
                except:
                    result.append(['Error'])
```

The rest of the code could be viewed in [my repository](https://github.com/alfredbowenfeng/audit-kit/tree/master/VAT-invoice) on GitHub. 

# User Interface
Similar to that of [Breakdown UI](https://bowenf.com/20180807/audit-breakdown-ui/), I created a user interface as well. The code could be viewed in [my repository](https://github.com/alfredbowenfeng/audit-kit/tree/master/VAT-invoice) on GitHub. 

# Creating An Executable File
Normally, pyinstaller could create files directory with the following commands. 
```
$ cd D:\Projects\pyinstaller\
$ pyinstaller -w -F --icon="D:\Projects\pyintaller\icon.ico" D:\Projects\pyinstaller\VAT-Invoices-Scanning-UI.py
```

The .exe file was generated succesfully. However, it could not be opened because of failure to load the script. After some time looking for reasons on the Internet, I realized that the binary files from the package `pyzbar` could not be located by pyinstaller. I saved a screenshot of the problem I had encountered. 
![Script Load Failure](/images/audit-vat-invoices-scanning-ui/script-load-failure.png)


I eventually solved the problem with the following commands instead, by including the two binary files (.dll files) when generating the executable file. 
```
$ cd D:\Projects\pyinstaller\
$ pyinstaller -w -F --i="D:\Projects\pyinstaller\ico.ico" --add-binary "C:\Program Files\Pytho
n36\Lib\site-packages\pyzbar\libiconv.dll;." --add-binary "C:\Program Files\Python36\Lib\site-packages\pyzbar\libzbar-64
.dll;." D:\Projects\pyinstaller\VAT-Invoices-Scanning-UI.py
```

Now the application could be opened and run properly. 
![VAT Invoices Scanning UI](/images/audit-vat-invoices-scanning-ui/vat-invoices-scanning-ui.png)