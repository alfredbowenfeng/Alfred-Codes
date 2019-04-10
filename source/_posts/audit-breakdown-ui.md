---
title: Breakdown Extraction UI Development
date: 2018-08-07 18:53:02
tags:
	- Audit
	- Python
categories: Data
---

# Original Program
Months before, I had developed [a Python program](https://bowenf.com/20180508/audit-breakdown/) to extract data from Excel workbooks, to organize them, and to write the breakdown into a new workbook. 

Most audits majored in Accounting or other Business-related subjects at university. Naturally, some of them might not be that familiar with programming. 

Therefore, making a user interface and creating an executable file would really make things easier for them. 

<!-- more -->

# tkinter for User Interface
The package was called 'tkinter' in Python 3, but 'Tkinter' in Python 2. I used `grid()` function to organize labels, buttons and mesages. 
```
# UI
root = Tk()
root.title('Application Title')
# Blank
l_bnk = Label(root, text='')
l_bnk.grid(row=0, columnspan=2)	
```

## Labels and Entry Boxes
For every pair of one label and one entry box, I wrote four lines of code, two for the label and the other two for the entry box. Here is an example of two pairs: A and B. 
```
# A
l_A = Label(root, text='A:')
l_A.grid(row=1, sticky=W)
e_A = Entry(root, width=50)
e_A.grid(row=1, column=1 sticky=E)
# B
l_B = Label(root, text='B:')
l_B.grid(row=2, sticky=W)
e_B = Entry(root, width=50)
e_B.grid(row=2, column=1 sticky=E)
```

## Execute Button
The 'Execute' button gave allowance to the user to start the program after inputting information. 
```
# Execute
b_A = Button(root, text='Execute', command=Execute, width=25)
b_A.grid(row=9, columnspan=2)
# Result
l_msg = Label(root, text='')
l_msg.grid(row=10, columnspan=2)	
```

## Responsive Text
In order to inform the user whether the program was completed or met problems, I defined a new `Execute()` function. A message 'Completed' or 'Error' will appear in the `l_msg` label, after the program stopped. 
```
def Execute():
	try:
		Selection()
		l_msg['text'] = 'Completed'
	except:
		l_msg['text'] = 'Error'
```

# PyInstaller for Creating An Executable File
I referred to the [PyInstaller Manual](https://pyinstaller.readthedocs.io/en/v3.3.1/) for reference. Here is some information about the options and paths. 
{% table %}
| Object or Option | Path or Function |
| --- | --- |
| Py File Path | D:\\Projects\\pyinstaller\\FileName.py |
| Icon | D:\\Projects\\pyinstaller\\icon.ico |
| -F | Creating one-file bundled executable. |
| -w | Disabling a terminal window. |
| --i | Using a .ico file as the icon. |
{% endtable %}

After installing PyInstaller with `pip`, I generated and tested the application immediately, as I entered the following commands in the Windows Powershell. 
```
$ cd D:\Projects\pyinstaller\
$ pyinstaller -w F --icon="D:\Projects\pyintaller\icon.ico" D:\Projects\pyinstaller\Breakdown-Value-UI.py
$ pyinstaller -w F --icon="D:\Projects\pyintaller\icon.ico" D:\Projects\pyinstaller\Breakdown-Link-UI.py
```

This process would generate three folders in the current directory: `__pycache__`, `dist`, and `build`. The executable file would be generated in the `dist` folder. 

Let's take a look at the user interface, which looks simple but is quite easy to understand. 
![Breakdown by Value UI](/images/audit-breakdown-ui/breakdown-value-ui.png)

![Breakdown by Link UI](/images/audit-breakdown-ui/breakdown-link-ui.png)
