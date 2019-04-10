---
title: Ubuntu Installation with Some Applications
date: 2018-07-04 13:45:52
tags:
	- Ubuntu
	- TensorFlow
categories: PC
---

# Ubuntu 18.04 Installation
I installed Ubuntu 18.04 through a USB stick. See the guide to creating a bootable USB stick on [Windows](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0) or [macOS](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0). 

I prepared a brand new SanDisk SSD for the installation of Ubuntu. Therefore, I chose "Erase disk and install Ubuntu". Do not worry about the warnings. Continuing with this option will not erase all your disks but will erase all the content on the disk you select in the next step. 

<!-- more -->

# Some Useful Tools and Applications

## pip3
The most frequent way I used to install applications on Ubuntu is as the following. 
```
$ sudo apt-get install python3-pip
```

## Anaconda
Download Anaconda for Linux from [here](https://www.anaconda.com/download/#linux) and type the following command in the Terminal to start installation. 
```
$ sudo bash ~/Downloads/Anaconda3-5.2.0-Linux-x86_64.sh
```

After installing CUDA and setting up the GPU-Boosting environment as [this article](https://alfredbowenfeng.github.io/20180715/ubuntu-cuda-installation), I installed TensorFlow packages from [Tsinghua Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/) with the following commands. 
```
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
$ conda config --set show_channel_urls yes
$ conda install tensorflow-gpu==1.8.0
$ conda install keras-gpu==2.2.0
```

## Sublime Text 3
To install Sublime Text 3, follow the [guide](https://www.sublimetext.com/docs/3/linux_repositories.html) on the official website or alternatively type the following commands in the Terminal. 
```
$ wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
$ sudo apt-get install apt-transport-https
$ echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
$ sudo apt-get update
$ sudo apt-get install sublime-text
```

## SS with Chacha
This is not supposed to be explained. 
```
$ sudo apt install libsodium-dev
$ pip3 install git@github.com:shadowsocks/shadowsocks/archive.zip -U
```

Just for reference, a json file should be created. 
```
{
	"server": "xxx.xxx.xxx.xxx",
	"server_port": xxxxx,
	"password": "xxxxxxxxxxxx",
	"method": "chacha20-ietf-poly1305",
	"local_address": "127.0.0.1",
	"local_port": 1080,
	"timeout": 300
}
```

# Possible Problems

## Apt-get Lock
Sometimes, the system may lock apt-get due to unfinished processes. Simply solve them with the following commands typed in the Terminal. 
```
$ sudo rm /var/cache/apt/archives/lock
$ sudo rm /var/lib/dpkg/lock
```

## Exfat not Supported
Solve this problem by typing the following command in the Terminal. 
```
$ sudo apt-get install exfat-fuse exfat-utils
```