---
layout: post
title: Inception v1
key: 20180406
tags: 
  - 论文精读
  - CNN
  - Computer Vision
---


```
Going deeper with convolutions
```

## 前言

在`ILSVRC2014`上最耀眼的除了VGG，那就非GoogLeNet莫属了，它与VGG类似的地方就是，关注于模型的深度，使模型倾向于deeper，与VGG不同的是，它的尝试更加新颖。从2014年至今，GoogLeNet经历了Inception v1到v4，以及Inception-ResNet的发展，也证明了Inception最初思想的潜力。今天我们就来看看GoogLeNet Inception V1的框架和效果。