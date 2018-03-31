---
layout: post
title: Anaconda & Libraries
key: 20180318
tags: 
  - 环境配置
  - 教程帖
  - 踩过的坑
---

## Anaconda
> 管理包和环境的 Python 工具

### 安装

下载并运行：
```
sudo bash Anaconda3-xxx-Linux-x86_64.sh
```
* 添加环境变量处选择 `yes`

### 包管理

查看已安装的包
```
conda list
```

安装包
```
conda install package_name
```

卸载包
```
conda remove package_names
```

更新包
```
conda update package_name
```

### 环境管理

查看已创建的环境
```
conda env list 
```

创建环境
```
conda create -n env_name
```

创建环境并制定 Python 版本
```
conda create -n env_name python=3.6
```

激活环境
```
source activate env_name
```

退出环境
```
source deactivate
```

删除环境
```
conda env remove -n env_name
```

## Libraries
> 利用 Anaconda 可以轻松的隔离不同版本的不同框架

### TensorFlow

创建 TensorFlow 环境，并激活：
```
conda create -n tensorflow python=3.6
source activate tensorflow
```

下载对应版本的 TensorFlow，并安装：
```
pip install tf-xxx-linux_x86_64.whl
```

### MXNet

创建 MXNet 环境，并激活：
```
conda create -n mxnet python=3.6
source activate mxnet
```

直接安装 MXNet:
```
pip install mxnet-cu80
```

### Pytorch

创建 Pytorch 环境，并激活：
```
conda create -n pytorch python=3.6
source activate pytorch
```

直接安装 MXNet:
```
conda install pytorch torchvision -c pytorch
```

### Others

其余框架如 Keras、CNTK 等，都可以尝试

