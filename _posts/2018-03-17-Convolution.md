---
layout: post
title: 卷积
key: 20180317
tags: 
  - MXNet & Gluon
  - CNN
  - Deep Learning
---

## 卷积层

```
input_size: i  
kernel_size: k  
output_size: o  
padding: p  
dilate: d  
stride: s  
```

```
o = floor((i+2*p-d*(k-1)-1)/s)+1
```

### 1. No padding, no strides

![no padding, no strides](http://zh.gluon.ai/_images/no_padding_no_strides.gif)

``` Python
from mxnet import nd

w = nd.arange(9).reshape((1,1,3,3))
b = nd.array([1])
data = nd.arange(16).reshape((1,1,4,4))
out = nd.Convolution(data, w, b, kernel=w.shape[2:], num_filter=w.shape[1])

print('input:', data, '\n\nweight:', w, '\n\nbias:', b, '\n\noutput:', out)
```
```
input:
[[[[ 0.  1.  2.]
   [ 3.  4.  5.]
   [ 6.  7.  8.]]]]
<NDArray 1x1x3x3 @cpu(0)>

weight:
[[[[ 0.  1.]
   [ 2.  3.]]]]
<NDArray 1x1x2x2 @cpu(0)>

bias:
[ 1.]
<NDArray 1 @cpu(0)>

output:
[[[[ 20.  26.]
   [ 38.  44.]]]]
<NDArray 1x1x2x2 @cpu(0)>
```