---
title: Ubuntu Installation with a GPU-Boosting Environment (CUDA)
date: 2018-07-15 16:43:49
tags:
	- Ubuntu
	- TensorFlow
categories: PC
---

# Ubuntu Desktop Version Selection
Currently, there are three versions Ubuntu Desktop recommended: [18.04](https://www.ubuntu.com/download/desktop) and [16.04](http://cdimage.ubuntu.com/netboot/16.04/?_ga=2.241279872.386305975.1532259090-534471948.1532259090). However, on the [NVIDIA official CUDA download page](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu), it says CUDA only supports Ubuntu 16.04 and 17.10. I encountered tons of problems when installing an appropiate version. On my PC, I firstly installed 18.04, secondly reinstalled 16.04, thirdly reinstalled 17.10, fourthly reinstalled 16.04, and reinstalled 18.04 finally. 

In the end, I sccuessfully installed CUDA and enabled GPU-Boosting on Ubuntu 18.04. So if you are to install CUDA and enable GPU-Boosting on Ubuntu on your own PC, I hope this article would offer you some help. 

<!-- more -->

**Updated on 22th July 2018:** IMPORTANT! Be sure to turn off the installations of updates from anywhere. Set "Automatically check for updates" as "Never". Once I installed Ubuntu software update, the GPU driver was set from a NVIDIA 390 driver to a nouveau one immediately. I failed to blacklist the nouveau driver and to change to the NVIDIA driver. As a result, my GPU could not be recognized by TensorFlow after the installation of the update. 

# GPU-Boosting Requirement
According to [TensorFlow's guide](https://www.tensorflow.org/install/install_linux#tensorflow_gpu_support), the following are required. 

> **TensorFlow GPU support**
To install TensorFlow with GPU support, configure the following NVIDIA® software on your system:
- [CUDA Toolkit 9.0](http://nvidia.com/cuda). For details, see [NVIDIA's documentation](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/). Append the relevant CUDA pathnames to the LD_LIBRARY_PATH environmental variable as described in the NVIDIA documentation.
- [cuDNN SDK v7](https://developer.nvidia.com/cudnn). For details, see [NVIDIA's documentation](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/). Create the CUDA_HOME environment variable as described in the NVIDIA documentation.
- A GPU card with CUDA Compute Capability 3.0 or higher for building TensorFlow from source. To use the TensorFlow binaries, version 3.5 or higher is required. See the [NVIDIA documentation](https://developer.nvidia.com/cuda-gpus) for a list of supported GPU cards.
- [GPU drivers](https://www.nvidia.com/Download/index.aspx?lang=en-us) that support your version of the CUDA Toolkit.

# Installation

## Ubuntu 18.04
I installed Ubuntu 18.04 through a USB stick. See the guide to creating a bootable USB stick on [Windows](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0) or [macOS](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0). 

I prepared a brand new SanDisk SSD for the installation of Ubuntu. Therefore, I chose "Erase disk and install Ubuntu". Do not worry about the warnings. Continuing with this option will not erase all your disks but will erase all the content on the disk you select in the next step. 

## NVIDIA Driver
After the installation of Ubuntu 18.04, the OS system ran an X.org driver. I opened "Software and Updates", selected "Additional Drivers", and chose to use nvidia-driver-390 (proprietary, tested) instead of X.Org X server. No further installation is required. 

## CUDA and cuDNN Version Selection
For CUDA 9, there are CUDA 9.2, 9.1 and 9.0 available. For cuDNN, there are v7.1.2 and v7.0.5 available. It is said that cuDNN v7.0.5 is currently the most comprehensive and complete version, so I chose cuDNN v7.0.5 to avoid possible trouble since I was installing CUDA on an unsupported OS (Ubuntu 18.04 is not supported by CUDA according to NVIDIA). Also, cuDNN only supports CUDA 9.1 and 9.0, so I chose CUDA 9.1 without second thought. 

## Downgrading GCC
CUDA 9.1 only supports GCC 6.3.0 for Ubuntu 17.10 and 5.3.1 for 16.10 and any former version, while GCC 7.3 will be installed during the Ubuntu 18.04 installation. According to CUDA Toolkit 9.1 [Online Documentation](https://docs.nvidia.com/cuda/archive/9.1/cuda-installation-guide-linux/index.html), GCC compiler is required for development. 

> The `gcc` compiler is required for development using the CUDA Toolkit. It is not required for running CUDA applications. It is generally installed as part of the Linux installation, and in most cases the version of gcc installed with a supported version of Linux will work correctly.

Therefore, I must downgrade GCC. According to a Chinese blogger who had tried and succeeded installing CUDA 9.1 on Ubuntu 18.04, I downgraded GCC 7.3 to 4.8 as was instructed in the [article](https://blog.csdn.net/u010801439/article/details/80483036). 

```
$ sudo apt-get install gcc-4.8
$ sudo apt-get install g++-4.8
$ cd /usr/bin
$ sudo mv gcc gcc.bak # backup
$ sudo ln -s gcc-4.8 gcc
$ sudo mv g++ g++.bak # backup
$ sudo ln -s g++-4.8 g++
```

## CUDA 9.1
Download CUDA 9.1 [here](https://developer.nvidia.com/cuda-91-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1604&target_type=runfilelocal), and follow the instructions given. There are a base installer and three patches, which should be installed in turn. 

**Warning: The NVIDIA driver has already been installed earlier, so type "no" when the CUDA installer asks whether to install NVIDIA driver.**

## cuDNN 7.0.5
Download cuDNN 7.0.5 [here](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/v7.0.5/prod/9.1_20171129/cudnn-9.1-linux-x64-v7), and follow the instructions of the [online document](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/#installlinux-tar). 
```
$ tar -xzvf ~/Downloads/cudnn-9.1-linux-x64-v7.tgz
$ cd ~/Downloads/
$ sudo cp cuda/include/cudnn.h /usr/local/cuda/include
$ sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

# Testing Available GPUs
There is several ways to test whether GPU-Boosting is enabled or not. You are able to see your GPU the list of devices through the following command in Jupyter Notebook. 
```
import tensorflow as tf
if tf.test.gpu_device_name():
    print('Default GPU Device: {}'.format(tf.test.gpu_device_name()))
else:
    print("Please install GPU version of TF")
```