---
title: Ubuntu Installation with Remote Notebook
date: 2018-07-15 18:39:46
tags:
	- Ubuntu
categories: PC
---

# Ubuntu, CUDA and Anaconda
After I had installed Ubuntu, CUDA and Anaconda on it, [Enkai](https://enkaiji.com) recommended me to set up a Notebook server that could be remotely accessed, so that I would be able to connect to my Notebook server and develop deep learning, even if I was outside with a laptop with a weak performance. 

Please see [Ubuntu Installation with Some Applications](https://bowenf/20180704/ubuntu-installation) and [Ubuntu Installation with a GPU-Boosting Environment (CUDA)](https://bowenf/20180715/ubuntu-cuda-installation) for further information about the installation of Ubuntu, CUDA and Anaconda. 

<!-- more -->

# Notebook Config Settings

## Password Generation
I generated the password by the following commands in the Terminal. The output varies from password inputted from "In [2]". 
```
$ ipython
In [1]: from notebook.auth import passwd

In [2]: passwd()
Enter password:
Veryfy password:

Out[2]: 'sha1:xxxxxxxxxx'
```

## Creating the Config File
Then I created a file named `jupyter_notebook_config.py` with Sublime Text 3 in `/home/bowenfeng/.jupyter/`, and put the following codes. If you want to set up a Notebook server as well, you can customize your own port instead of "8888".
```
c = get_config()

c.NotebookApp.ip = '*' 
c.NotebookApp.password = u'sha1:xxxxxxxxxx'
c.NotebookApp.open_browser = False 
c.NotebookApp.port = 8888
```

## Local Testing
After saving the `jupyter_notebook_config.py` file, I turned on Jupyter notebook with the following commands typed in the Terminal. 
`$ ipython notebook`

It says "The Jupyter Notebook is running at: https://bowen-8700-1080-ubuntu:8888/". I went to "localhost:8888" for the testing. Right now, it did not require the password because I was visiting "localhost" So it worked! 

# Router Forwarding Settings
My apartment is connected to a China Telecom network environment. All of the devices in my apartment are connected to my Linksys router, and my router is connected to the Telecom Modem (电信猫). That is to say, the Telecom Modem is the first router and my router is the second one. As a result, I have to configurate both routers in their management pages. The management page of the Telecom Modem was "192.168.1.1", while the one of my Linksys router was "192.168.1.2". 

Acquire the network status from the following commands in the Terminal. 
```
$ sudo apt install net-tools
$ ifconfig
```

The external IP assigned by China Telecom is "101.228.190.41". The Telecom Modem assigned "192.168.1.2" to my Linksys router, while my Linksys router assigned "10.194.206.100" to my PC. 

## Internal IP
Internal IP addresses are assigned by my Linksys router. For example, the router assigned "10.194.206.100" to my PC. So I tested again whether my server was working or not by going to "10.194.206.100:8888". As was expected, it worked! 
![Internal Connection](/images/ubuntu-remote-notebook/internal-connection.png)

## Enabling Forwarding on My Linksys Router
In order to make the server accessible from the devices in other network environments, I had to set up a forwarding in the Linksys router management page ("192.168.1.2") like the following. 
{% table %}
| Application name | External Port | Internal Port | Protocol | Device IP# | Enabled |
| --- | --- | --- | --- | --- | --- |
| Jupyter | 8888 | 8888 | Both | 10.194.206.100 | True |
{% endtable %}

## Enabling Virtual Host on the Telecom Modem
Apart from the previous step, I had to set up a forwarding in the Telecom Modem management page ("192.168.1.1") like the following. 
{% table %}
| 服务器名 | 外部初始端口 | 外部终止端口 | 协议 | 内部初始端口 | 内部终止端口 | 服务器IP地址 | WAN接口 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Router | 8888 | 8888 | TCP/UDP | 8888 | 8888 | 192.168.1.2 | ppp1.3 |
{% endtable %}

Note that if you do not have the super-admin management account and password for your Telecom Modem, please refer to [this article](https://zhuanlan.zhihu.com/p/30242365) on Zhihu. 

# Final Testing
After finishing the forwarding settings for both of the routers, the server should work. Let's start the server and test it by going to "101.228.190.41:8888" in the browser. It worked! 
![Finished](/images/ubuntu-remote-notebook/finish.png)