---
layout: post
title: Hybridize：Imperative & Symbolic 
key: 20180330
tags: 
  - MXNet & Gluon
  - Deep Learning
---

> 参考 [Gluon官方教程](https://zh.gluon.ai/chapter_gluon-advances/hybridize.html)

## 命令式编程 VS 符号式编程

考虑这段命令式编程的代码：

```python
def add(a, b):
    return a + b

def fancy_func(a, b, c, d):
    e = add(a, b)
    f = add(c, d)
    g = add(e, f)
    return g

fancy_func(1, 2, 3, 4)
```
```
10
```

在运行这段代码时，程序按照命令来执行运算：在运行 `e = add(a, b)` 时，Python 会做加法运算并将结果存储在变量 `e` ，从而令程序的状态发生了改变。

采用符号式编程改写：

```python
def add_str():
    return '''
def add(a, b):
    return a + b
'''

def fancy_func_str():
    return '''
def fancy_func(a, b, c, d):
    e = add(a, b)
    f = add(c, d)
    g = add(e, f)
    return g
'''

def evoke_str():
    return add_str() + fancy_func_str() + '''
print(fancy_func(1, 2, 3, 4))
'''

prog = evoke_str()
print(prog)
y = compile(prog, '', 'exec')
exec(y)
```
```
def add(a, b):
    return a + b

def fancy_func(a, b, c, d):
    e = add(a, b)
    f = add(c, d)
    g = add(e, f)
    return g

print(fancy_func(1, 2, 3, 4))

10
```

与命令式编程的不同之处在于，符号式编程通常在计算流程完全定义好后才被执行：在运行 `e = add(a, b)` 时，程序不产生计算，只返回计算图来描述整个计算过程；直到最后再编译完整的计算图并运行。因此，符号式编程的程序需要下面三个步骤：
* 定义计算图
* 把计算图编译成可执行的程序
* 给定输入，调用编译好的程序执行

## 混合式编程
* 符号式编程：TensorFlow, Theano
* 命令式编程：PyTorch，Chainer
* 混合式编程：Gluon

```python
from mxnet.gluon import nn
from mxnet import nd

def get_net():
    net = nn.HybridSequential()
    with net.name_scope():
        net.add(
            nn.Dense(256, activation="relu"),
            nn.Dense(128, activation="relu"),
            nn.Dense(2)
        )
    net.initialize()
    return net

x = nd.random.normal(shape=(1, 512))
net = get_net()
print(net(x))

net.hybridize()
print(net(x))
```
```
before:  
[[ 0.03969435 -0.10410519]]
<NDArray 1x2 @cpu(0)>

after:  
[[ 0.03969435 -0.10410519]]
<NDArray 1x2 @cpu(0)>
```

先使用 `HybridSequential` 类来创建网络，然后调用 `hybridize` 函数来编译和优化 `HybridSequential` 的实例。此时输出结果不变。

在网络计算出输出之后，

## 符号式编程

```python
from mxnet import sym

x = sym.var('data')
print(x)
y = net(x)
print(y)
x2 = nd.random.normal(shape=(1, 512))
y2 = net(x2)
print(y2)
```
```
<Symbol data>

<Symbol hybridsequential2_dense2_fwd>

[[-0.1135736  -0.05925979]]
<NDArray 1x2 @cpu(0)>
```
对比两次结果可以发现，当网络输入为 `Symbol` 类的变量时，输出也为 `Symbol` ，可以理解为输出了整个函数（网络结构）。因此，符号式编程有利于模型的迁移和复用。

```python
net(x).save('model.json')
net.save_params('model.params')
```

或采用

```python
net.export('model')
```

## How Hybridize Works

```python
from mxnet import gluon

class Net(gluon.HybridBlock):
    def __init__(self, **kwargs):
        super(Net, self).__init__(**kwargs)
        with self.name_scope():
            self.fc1 = nn.Dense(256)
            self.fc2 = nn.Dense(128)
            self.fc3 = nn.Dense(2)

    def hybrid_forward(self, F, x):
        # F is a function space that depends on the type of x
        # If x's type is NDArray, then F will be mxnet.nd
        # If x's type is Symbol, then F will be mxnet.sym
        print(F)
        print(x)
        x = F.relu(self.fc1(x))
        print(x)
        return self.fc2(x)
```
使用 `HybridBlock` 构建网络，相比原来的 `Block` 实现的 `forward` ，在 `hybrid_forward` 中需要增加一个输入 `F` ，代表输入数据的格式，即：NDArray或Symbol，这样可以根据 `F` 来调用相应的函数。

```python
net = HybridNet()
net.initialize()
x = nd.random.normal(shape=(1, 4))
y = net(x)
```

```
<module 'mxnet.ndarray' from '/home/bme/anaconda3/envs/mxnet/lib/python3.6/site-packages/mxnet/ndarray/__init__.py'>

[[-0.86531955 -0.58072501 -0.033391    0.46204349]]
<NDArray 1x4 @cpu(0)>

[[ 0.          0.09821007  0.          0.          0.05462556  0.
   0.03768773  0.05834861  0.07981843  0.        ]]
<NDArray 1x10 @cpu(0)>
```

实例化一个样例，然后可以看到默认 `F` 是使用ndarray

再运行一次会得到同样的结果

```python
y = net(x)
```
```
<module 'mxnet.ndarray' from '/home/bme/anaconda3/envs/mxnet/lib/python3.6/site-packages/mxnet/ndarray/__init__.py'>

[[-0.86531955 -0.58072501 -0.033391    0.46204349]]
<NDArray 1x4 @cpu(0)>

[[ 0.          0.09821007  0.          0.          0.05462556  0.
   0.03768773  0.05834861  0.07981843  0.        ]]
<NDArray 1x10 @cpu(0)>
```

接下 `hybridze()` 

```python
net.hybridize()
net(x)
```
```
<module 'mxnet.symbol' from '/home/bme/anaconda3/envs/mxnet/lib/python3.6/site-packages/mxnet/symbol/__init__.py'>
<Symbol data>
<Symbol hybridnet0_relu0>
```
可以看到：
* `F` 变成了 `symbol` ，同时 `x` 和 `隐藏层` 数据均变为 `symbol` 。
* 即使输入数据还是NDArray的类型，但 `hybrid_forward` 里不论是输入还是中间输出，全部变成了Symbol

再运行一次
```python
net(x)
```
可以看到没有任何输出，意味着网络生成以后就不再改变。这是因为第一次运行 `net(x)` 的时候，会先将输入替换成Symbol来构建符号式的程序，之后运行的时候系统将不再访问Python的代码，而是直接在C++后端执行这个符号式程序。这是为什么hybridze后会变快的一个原因。
