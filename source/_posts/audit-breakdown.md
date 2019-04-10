---
title: Breakdown Extraction in Auditing
date: 2018-05-08 20:03:02
tags:
	- Audit
	- Python
categories: Data
---

# My Motive
This afternoon, I went to attend the Partner Interview at EY Shanghai Office, after my 2-month winter internship. During the interview, Lillian Zhang, one of the RCPT partners and my interviewer, asked me whether I had developed anything for my lovely team since I knew a little about programming. 

In fact, I developed nothing during my internship, because usually I woke up at 8 in the morning, went to the client's immediately, worked, worked over time, sometimes to 2AM, once even to 3AM, and went back home to bed directly. By the way, under most circumstances, I was the first to go home. Audits did work hard. Salute to all of you! :+1:

I thought a lot about it on my way home after the interview and decided to try developing something. Extracting data, from a large number of Excel workbooks with the identical format, took me the most time when I worked for EY. Due to the confidentiality of auditing procedures, I will only present externally accessible information here in my blog. 

<!-- more -->

# Background
At Grand Baoxin Group Limited, 176 4S stores were involved this year. Financial manager(s) from each store provided us with a strictly-formatted workbook including dozens of sheets which we called a PBC (Provided by Client). 

Sometimes we needed to compare and verify the data provided by the individual store with those done by the headquarters. As a result, extracting and organizing data became pivotal. 

As was told to me by one of the friendly senior managers, years ago, audits organized hundreds of thousands of data manually of each account. Later, audits started to use VBA to extract and organize data. Compared to manual operations, it became much easier; however, VBA took much time when running, and users just could not do anything because applications did not response when VBA was occupying CPU and Memory. 

Thus writing a Python file as substitution for VBA first came to my mind. 

# Accessing an xlsx File via Python
After looking into the methods on the Internet, I decided to use two packages -- `xlrd` and `xlsxwriter` -- to both extract data from and write into .xlsx files. Without any doubt, `xlrd` was to be used for extraction and `xlsxwriter` was to be used for export. 
```
import xlrd, xlsxwriter
```

## Listing All Excel Files from a Directory
I used the `os` package, defined a directory, listed all files, and made selections. 
```
ImportFileDirectory = '/Users/bowenfeng/Desktop/PBC/'
FileNameList = [f for f in FileNameList if ((f[-4:] == 'xlsx') and (f[:2] != '._') and (f[:2] != '~$'))]
```

## Opening a Sheet from a Workbook
It was quite easy to open a workbook. Simply use the `xlrd.open()` command, with an argument `encoding_override='utf-8'` to avoid Chinese character problems on Windows. By the way, I originally developed this program on macOS, and now I am blogging about it on Ubuntu. 

To open a certain sheet in a workbook is quite easy as well. Simply use the `.sheet_by_name()` command. The example is presented below. 
```
ExcelFile = xlrd.open(ImportFileDirectory+FileNameList[0], encoding_override='utf-8')
Sheet = ExcelFile.sheet_by_name('testsheet')

```

## Extracting the Value from A Cell
To extract the value from a cell, simply use the `.cell_value(i, j)` command. `.cell_value(0, 0)` responded the value of 'A1', `.cell_value(0, 26)` did that of 'AA1', `.cell_value(100, 0)` did that of 'A99', and so on. It was quite similar to Python, because it started from 0 instead of 1. 

There are four situations: 
- 1. to extract only one cell; 
- 2. to extract cells from one column but multiple rows; 
- 3. to extract cells from one row but multiple columns; 
- 4. to extract cells from multiple columns and multiple rows. 

Consequently, I defined four functions: Files_Row_Column, Files_Row_Columns, Files_Rows_Column, and Files_Rows_Columns, so as to cope with four situations.

### Variables Input
Variables should be defined before the situation determination process, as the following presents. 
```
CellColumnPool = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
StartCellColumn = input('StartCellColumn')
if len(StartCellColumn) == 1:
	StartCellColumn_N = CellColumnPool.index(StartCellColumn)
elif len(StartCellColumn) == 2:
	StartCellColumn_N = (CellColumnPool.index(StartCellColumn[0]) + 1) * 26 + CellColumnPool.index(StartCellColumn[1])
else:
	print('Not Supported')
StartCellRow_N = input('StartCellRow')
EndCellColumn = input('EndCellColumn')
if len(EndCellColumn) == 1:
	EndCellColumn_N = CellColumnPool.index(EndCellColumn) + 1
elif len(EndCellColumn) == 2:
	EndCellColumn_N = (CellColumnPool.index(EndCellColumn[0]) + 1) * 26 + CellColumnPool.index(EndCellColumn[1]) + 1
else:
	print('Not Supported')
```

### Situation Determination
Besides, I defined another function for determining which function from the four to apply. 
```
if StartCellRow_N + 1 == EndCellRow_N:
	if StartCellColumn == EndCellColumn:
		Files_Row_Column()
	else:
		Files_Row_Columns()
else:
	if StartCellColumn == EndCellColumn:
		Files_Rows_Column()
	else:
		Files_Rows_Columns()
```

### Files_Row_Column
Only extracting one cell value from each workbook was simple. 
```
result = []
for i in range(0, FileNameList):
	ExcelFile = xlrd.open(ImportFileDirectory+FileNameList[i], encoding_override='utf-8')
	Sheet = ExcelFile.sheet_by_name('testsheet')
	result.append([FileNameList[0], Sheet.cell_value(StartCellRow_N, StartCellColumn_N)])
```

### Files_Row_Columns
This function was easy to write as well, because in the result, only one row would be taken for each workbook. 
```
result = []
for i in range(0, FileNameList):
	ExcelFile = xlrd.open(ImportFileDirectory+FileNameList[i], encoding_override='utf-8')
	Sheet = ExcelFile.sheet_by_name('testsheet')
	row = [FileNameList[i]]
	for j in range(StartCellColumn_N, EndCellColumn_N):
		row.append(Sheet.cell_value(StartCellRow_N, j))
	result.append(row)
```

### Files_Rows_Column
This function was the same simple but a little harder to understand, because I decided to transpose columns to rows. 
```
result = []
for i in range(0, FileNameList):
	ExcelFile = xlrd.open(ImportFileDirectory+FileNameList[i], encoding_override='utf-8')
	Sheet = ExcelFile.sheet_by_name('testsheet')
	row = [FileNameList[i]]
	for j in range(StartCellRow_N, EndCellRow_N):
		row.append(j, StartCellColumn_N)
	result.append(row)
```

### Files_Rows_Columns
This function was more complicated than the previous three. 
```
result = []
for i in range(0, FileNameList):
	ExcelFile = xlrd.open(ImportFileDirectory+FileNameList[i], encoding_override='utf-8')
	Sheet = ExcelFile.sheet_by_name('testsheet')
	for j in range(StartCellRow_N, EndCellRow_N):
		row = [FileNameList[i]]
		for k in range(StartCellColumn_N, EndCellColumn_N):
			row.append(j, k)
		result.append(row)
```

## Exporting Breakdown to a Workbook
The data extracted from multiple cells of multiple workbooks were organized in the `result` variable. To export `result` to an Excel file, I defined an `ExportExcel()` function as well as an `ExportExcels()` function, for two situations. 
```
def ExportExcel(Breakdown):
	workbook = xlsxwriter.Workbook(ExportFilePath)
	worksheet = workbook.add_worksheet()
	for m in range(0, len(Breakdown)):
		for n in range(0, len(Breakdown[m])):
			worksheet.write(m, n, Breakdown[m][n])
	workbook.close()

def ExportExcels(Breakdown):
	workbook = xlsxwriter.Workbook(ExportFilePath)
	worksheet = workbook.add_worksheet()
	for l in range(0, len(Breakdown)):
		for m in range(0, len(Breakdown[l])):
			for n in range(0, len(Breakdown[l][m])):
				worksheet.write(l * len(Breakdown[l]) + m, n, Breakdown[l][m][n])
	workbook.close()
```

# Multi-Processing and Modularization
After some testing and editing, I found that the time it took to open a workbook by Python (either using xlrd or pandas) was actually longer than it did by VBA. Recommended by [Enkai](https://enkaiji.com), multi-processing was applied to it. 

## Package Import
I assigned a task of extracting each workbook to each `Process`, and retrieved the result through `Queue`. 
```
from multiprocessing import Process, Queue
```

## Functions Changes
I slightly changed the functions defined before. Take `Selection()` for instance. 
```
def Selection():
	global ImportFileDirectory, finals
	FileNameList = listdir(ImportFileDirectory)
	FileNameList = [f for f in FileNameList if ((f[-4:] == 'xlsx') and (f[:2] != '._') and (f[:2] != '~$'))]
	ProcessList = []
	q = Queue()
	if StartCellRow_N + 1 == EndCellRow_N:
		if StartCellColumn == EndCellColumn:
			for f in FileNameList:
				p = Process(target = Files_Row_Column, args = (q, f, ImportFileDirectory, SheetName, StartCellRow_N, StartCellColumn_N, ))
				ProcessList.append(p)
				p.start()
		else:
			for f in FileNameList:
				p = Process(target = Files_Row_Columns, args = (q, f, ImportFileDirectory, SheetName, StartCellRow_N, StartCellColumn_N, EndCellColumn_N, ))
				ProcessList.append(p)
				p.start()
	else:
		if StartCellColumn == EndCellColumn:
			for f in FileNameList:
				p = Process(target = Files_Rows_Column, args = (q, f, ImportFileDirectory, SheetName, StartCellRow_N, EndCellRow_N, StartCellColumn_N, ))
				ProcessList.append(p)
				p.start()
		else:
			for f in FileNameList:
				p = Process(target = Files_Rows_Columns, args = (q, f, ImportFileDirectory, SheetName, StartCellRow_N, EndCellRow_N, StartCellColumn_N, EndCellColumn_N, ))
				ProcessList.append(p)
				p.start()
	for p in ProcessList:
		finals.append(q.get())
	for p in ProcessList:
		p.join()
	if StartCellRow_N + 1 == EndCellRow_N:
		if StartCellColumn == EndCellColumn:
			ExportExcel(finals)
		else:
			ExportExcel(finals)
	else:
		if StartCellColumn == EndCellColumn:
			ExportExcel(finals)
		else:
			ExportExcels(finals)
```

The rest of the code could be viewed in [my repository](https://github.com/alfredbowenfeng/audit-kit/tree/master/Breakdown) on GitHub. 

## Specific Problems on Windows
On macOS or Linux, I could run the code with just the following. 
```
Selection()
```

However, on Windows, I encountered an error, and corrected it with the following modifications. 
```
from multiprocessing import Process, Queue, freeze_support()
if __name__ == '__main__':
	freeze_support()
	Selection()
```

# Breakdown by Link instead of Value
Another application of VBA was to "Paste Link". A cell's link in Excel was like a Excel function that directed a path to another cell. In Excel, trace by simply pressing `ctrl + [`.

The Excel function was like the following. 
```
='FilePath[SheetName]'!$Column$Row
```

This way, there was even no need to open workbooks one by one, which would definitely save lots of time. The code could be viewed in [my repository](https://github.com/alfredbowenfeng/audit-kit/tree/master/Breakdown) on GitHub.

I hope this article may be helpful. Also, in the future, I am to work on the user interface of these two programs. 