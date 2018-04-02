---
layout: post
title: Ubuntu & CUDA
key: 20180318
tags: 
  - 环境配置
  - 教程帖
  - 踩过的坑
---

## Ubuntu 16.04

没什么好说的，取个好名字

## Nvidia 显卡驱动

### 1. 卸载原有 Nvidia 驱动

```
sudo apt-get remove --purge nvidia*
```

### 2. 禁用 nouveau 驱动
> 禁用nouveau第三方驱动，之后也不需要改回来

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
如果没有屏幕输出，说明禁用nouveau成功

### 3. 禁用X-Window服务
> 关闭图形界面，进入命令行操作

```
sudo service lightdm stop
```
按 `Ctrl-Alt+F1` 进入命令行界面，输入用户名和密码登录

### 4. 安装驱动

给驱动run文件赋予执行权限：
```
sudo chmod +x NVIDIA-Linux-x86_64-xxx.run
```
执行安装，注意参数：
```
sudo ./NVIDIA-Linux-x86_64-384.59.run –no-opengl-files
```
* `–no-opengl-files`：只安装驱动文件，不安装 OpenGL 文件
 
### 5. 重启测试
重启Ubuntu
```
reboot
```

GPU的信息列表
```
nvidia-smi
```

GPU设置信息
``` 
nvidia-settings 
```

## 6. 屏幕分辨率

```
sudo xrandr --newmode "1600x900_60.00"  119.00  1600 1696 1864 2128  900 901 904 932 -hsync +vsync
sudo xrandr --addmode VGA-1 "1600x900_60.00"
```

## CUDA 8.0 & cuDnn 6

### 1. 禁用 nouveau 驱动
确认 nouveau 驱动已禁用：
```
lsmod | grep nouveau
```

### 2. 安装 CUDA 8.0

```
sudo ./cuda_xxx_linux.run --no-opengl-libs
```
* `--no-opengl-libs`：只安装驱动文件，不安装 OpenGL 库
* 注意 Driver 选择不安装，因为已经安装最新驱动
* Samples 选择不复制，因为安装目录下已存在

### 3. 安装 cuDnn 6

下载并解压 cudnn
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

### 4. 配置环境变量

```
sudo gedit /etc/profile 
```

在文件末尾添加两行:
```
export PATH=/usr/local/cuda-8.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda8.0/lib64:$LD_LIBRARY_PATH
```


```
source ~/.bashrc
```

### 5. 测试 Samples

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
