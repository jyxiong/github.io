---
layout: post
title: Ubuntu & CUDA
key: 20180221
tags: 
  - 环境配置
  - 教程帖
  - 踩过的坑
---

## Ubuntu 16.04

没什么好说的，取个好名字

## NVIDIA 显卡驱动
### 1. 下载 NVIDIA 驱动
前往 NVIDIA 官网下载[驱动](http://www.nvidia.cn/Download/index.aspx?lang=cn)  
注意对应版本，包括 _操作系统_ 和 _显卡_

### 2. 卸载原有 NVIDIA 驱动

```
sudo apt-get remove --purge nvidia*
```

### 3. 禁用 nouveau 驱动
> 禁用 nouveau 第三方驱动，之后也不需要改回来

```
sudo gedit /etc/modprobe.d/blacklist.conf
```
在文本最后添加：
```
blacklist nouveau
options nouveau modeset=0
```
然后执行：
```
sudo update-initramfs -u
```
重启后执行：
```
lsmod | grep nouveau 
```
如果没有屏幕输出，说明禁用 nouveau 成功

### 4. 禁用X-Window服务
> 关闭图形界面，进入命令行操作

```
sudo service lightdm stop
```
按 `Ctrl-Alt+F1` 进入命令行界面，输入用户名和密码登录

### 5. 安装驱动

给驱动 .run 文件赋予执行权限：
```
sudo chmod +x NVIDIA-Linux-x86_64-xxx.run
```
执行安装，注意参数：
```
sudo ./NVIDIA-Linux-x86_64-384.59.run –no-opengl-files
```
* `–no-opengl-files`：只安装驱动文件，不安装 OpenGL 文件
 
### 6. 重启测试
重启Ubuntu
```
reboot
```

GPU 的信息列表
```
nvidia-smi
```

GPU 设置信息
``` 
nvidia-settings 
```

### 7. 屏幕分辨率
*注意：根据自己的屏幕分辨率有不同的参数，我的屏幕是 1600 x 900，刷新频率 60 Hz*
```
sudo xrandr --newmode "1600x900_60.00"  119.00  1600 1696 1864 2128  900 901 904 932 -hsync +vsync
sudo xrandr --addmode VGA-1 "1600x900_60.00"
```

## CUDA 8.0 & cuDNN 6
### 1. 下载 CUDA 及 cuDNN
[CUDA](https://developer.nvidia.com/cuda-toolkit)  
*注意选择对应版本，`Installer Type` 选择 `runfile (local)`*  

[cuDnn](https://developer.nvidia.com/cudnn)  
*需要先注册，注意版本要与 CUDA 匹配*  

### 2. 禁用 nouveau 驱动
确认 nouveau 驱动已禁用：
```
lsmod | grep nouveau
```

### 3. 安装 CUDA 8.0

```
sudo ./cuda_xxx_linux.run --no-opengl-libs
```
* `--no-opengl-libs`：只安装驱动文件，不安装 OpenGL 库
* 注意 Driver 选择不安装，因为已经安装最新驱动
* Samples 选择不复制，因为安装目录下已存在

### 4. 安装 cuDNN 6

下载并解压 cuDNN
```
tar -zxf cudnn-6.0-linux-x64-v5.0-ga.tgz
```

把对应的文件复制到 CUDA 目录下：
```
cd cuda
sudo cp -P lib64/* /usr/local/cuda/lib64/
sudo cp -P include/* /usr/local/cuda/include/
sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
```

### 5. 配置环境变量

```
sudo gedit /etc/profile 
```

在文件末尾添加两行:
```
export PATH=/usr/local/cuda-8.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda8.0/lib64:$LD_LIBRARY_PATH
```

加载修改后的设置，永久生效
```
source ~/.bashrc
```

### 6. 测试 Samples

编译并测试设备 deviceQuery:
```
cd /usr/local/cuda-8.0/samples
sudo make
./deviceQuery
```

编译并测试带宽 bandwidthTest：
```
cd ../bandwidthTest
sudo make
./bandwidthTest
```
