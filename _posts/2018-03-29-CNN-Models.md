---
layout: post
title: Convolutional Neural Networks
key: 20180329
tags: 
  - MXNet & Gluon
  - CNN
  - Computer Vision
---

## About
所有代码均可[下载](https://github.com/jyxiong/cnn-models-mxnet)

## LeNet
```
LeCun, Y., Bottou, L., Bengio, Y., and Haffner, P. (1998). Gradient-based learning applied to document recognition.
```

> * CNN 的起源  
> * 接近现代 CNN

### 定义
* 3 个卷积层，1 个全连接层
* Max-Pooling 和 Average-Pooling
* RBF 输出 (Gaussian connection)

| layer  | input_size | kernel_size | strides | output_size   | num_kernel |
| :----: | :--------: | :---------: | :-----: | :-----------: | :--------: |
| c1     | 1\*32\*32  | 5\*5        | 1       | (32-5)/1+1=28 | 6          |
| s2     | 6\*28\*28  | 2\*2        | 2       | (28-2)/2+1=14 | 6          |
| c3     | 6\*14\*14  | 5\*5        | 1       | (14-5)/1+1=10 | 16         |
| s4     | 16\*10\*10 | 2\*2        | 2       | (10-2)/2+1=5  | 16         |
| c5     | 16\*5\*5   | 5\*5        | 1       | (5-5)/1+1=1   | 120        |
| f6     | 120\*1     | \           | \       | 84            | \          |
| output | 84         | \           | \       | 10            | \          |

### 实现
*注：该版本不是论文中的 LeNet，比如激活函数变为 ReLU，池化采用 Max_pooling 等*

```python
from mxnet.gluon import nn

def get_net(num_classes):
    net = nn.Sequential()
    with net.name_scope():
        net.add(
            # first conv
            nn.Conv2D(channels=6, kernel_size=5), 
            nn.Activation(activation='relu'),
            nn.MaxPool2D(pool_size=2, strides=2),
            # second conv
            nn.Conv2D(channels=16, kernel_size=5),
            nn.Activation(activation='relu'),
            nn.MaxPool2D(pool_size=2, strides=2),
            # first fullc
            nn.Flatten(),
            nn.Dense(500),
            nn.Activation(activation='relu'),
            # second fullc
            nn.Dense(num_classes)
        )
    return net
```

## AlexNet
```
Krizhevsky, A., Sutskever, I., and Hinton, G. E. (2012). ImageNet classification with deep convolutional neural networks.
```

> * 更大的数据库 —— ImageNet
> * 更快的处理器 —— GPU
> * 更深的网络 —— AlexNet

### 定义
* 5 个卷积层，3 个全连接层
* Data augmentation
* ReLU 激活函数
* Overlap Pooling
* Dropout
* Local Responce Normalization
* 多 GPU 训练

### 实现
*注：该版本为简化的 AlexNet，如去除了 LRN 层*

```python
from mxnet.gluon import nn

def get_net(num_classes):
    net = nn.Sequential()
    with net.name_scope():
        net.add(
            # stage 1
            nn.Conv2D(channels=96, kernel_size=11, strides=4),
            nn.Activation(activation='relu'),  
            nn.MaxPool2D(pool_size=3, strides=2),
            # stage 2
            nn.Conv2D(channels=256, kernel_size=5, padding=2),
            nn.Activation(activation='relu'),
            nn.MaxPool2D(pool_size=3, strides=2),
            # stage 3
            nn.Conv2D(channels=384, kernel_size=3, padding=1),
            nn.Activation(activation='relu'),
            nn.Conv2D(channels=384, kernel_size=3, padding=1),
            nn.Activation(activation='relu'),
            nn.Conv2D(channels=256, kernel_size=3, padding=1),
            nn.Activation(activation='relu'),
            nn.MaxPool2D(pool_size=3, strides=2),
            # stage 4
            nn.Flatten(),
            nn.Dense(4096),
            nn.Activation(activation='relu'),            
            nn.Dropout(.5),
            # stage 5
            nn.Dense(4096),
            nn.Activation(activation='relu'),            
            nn.Dropout(.5),
            # stage 6
            nn.Dense(num_classes)
        )
    return net
```

## VGG
```
Simonyan, K., and Zisserman, A. (2014). Very deep convolutional networks for large-scale image recognition. 
```

> * AlexNet 的改进
> * 非线性描述能力

### 定义
* 5段卷积层，3段全连接层
* 3\*3 的小尺寸卷积核组合替换 7\*7，参数只有一半
* Multi-scale 训练：训练多个分类器或多尺度输入
* Multi-scale 测试：滑动窗口，预测结果(多尺度、多窗口)平均

### 实现
```python
from mxnet.gluon import nn

def get_net(num_classes=1000, num_layers=11, batch_norm=False):
    """
    Parameters
    ----------
    num_classes : int, default 1000
        Number of classification classes.
    num_layers : int
        Number of layers for the variant of densenet. Options are 11, 13, 16, 19.
    batch_norm : bool, default False
        Use batch normalization.
    """ 
    # {num_layers: ((layers, kernel_size)))}
    vgg_arch = {11: ((1,64), (1,128), (2,256), (2,512), (2,512)),
                13: ((2,64), (2,128), (2,256), (2,512), (2,512)),
                16: ((2,64), (2,128), (3,256), (3,512), (3,512)),
                19: ((2,64), (2,128), (4,256), (4,512), (4,512))}
    # num of layers are 11, 13, 16, 19.
    if num_layers not in vgg_arch:
        raise ValueError('Invalide num_layers {}. Possible choices are 11,13,16,19.'.format(num_layers))
    architecture = vgg_arch[num_layers]    
    net = nn.Sequential()
    with net.name_scope():
        net.add(
            vgg_block(architecture, batch_norm),
            nn.Flatten(),
            nn.Dense(4096),
            nn.Activation(activation='relu'),            
            nn.Dropout(.5),
            nn.Dense(4096),
            nn.Activation(activation='relu'),            
            nn.Dropout(.5),
            nn.Dense(num_classes)
        )
    return net

def vgg_block(architecture, batch_norm):
    """ get different architecture of convolution layers """
    out = nn.Sequential()
    for (num_convs, channels) in architecture:
        out.add(vgg_stack(num_convs, channels, batch_norm))
    return out

def vgg_stack(num_convs, channels, batch_norm):
    out = nn.Sequential()
    for _ in range(num_convs):
        out.add(nn.Conv2D(channels=channels, kernel_size=3, padding=1))
        if batch_norm:
            out.add(nn.BatchNorm())
        out.add(nn.Activation(activation='relu'))
    out.add(nn.MaxPool2D(pool_size=2, strides=2))
    return out
```