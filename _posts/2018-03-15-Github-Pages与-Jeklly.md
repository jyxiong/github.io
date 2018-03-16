---
layout: post
title: GitHub Pages 与 Jekyll
key: 20180315
tags: 
  - 环境配置
  - 教程帖
  - 踩过的坑
---

> [GitHub Pages](https://pages.github.com/) 是一个静态网站托管服务，可直接从GitHub托管页面
> [Jekyll](https://jekyllrb.com/) 是一个静态站点生成器，它会根据网页源码生成静态文件
> 使用 GitHub Pages 和 Jekyll，可以自由 (**free**) 且免费 (**free**) 地制作网站

##  GitHub Pages
1. 注册一个 [GitHub](https://github.com/) 账号
2. 创建基于用户名的repository(仓库)：[jyxiong.github.io](https://github.com/jyxiong/jyxiong.github.io)
3. 拉取现有模板到仓库中，如 [Jekyll Themes](http://jekyllthemes.org/)
4. 根据模板提示修改配置文件 `_config.yml`

## Ruby
> Jekyll 需要安装 Ruby  

Windows 下直接下载集成好的 [RubyInstaller](https://rubyinstaller.org/downloads/) 来进行安装
注意选择正确的版本

## RubyGems
> 用 RubyGems 管理 Ruby 包  

RubyGems 是一个 Ruby 的包管理系统，类似于 Anaconda 与 Python 的关系。
下载 [RubyGems](https://rubygems.org/pages/download) 压缩包，解压到本地任意位置
```
$ cd yourpath to RubyGems
$ ruby setup.rb
```

## Jekyll
> 用 Jekyll 在本地浏览网站

```
$ gem install jekyll
$ cd you website path
$ jekyll serve
```
在浏览器打开 http://localhost:4000/ 即可在本地运行网站

## Bugs
### `gem install jekyll` 不能运行

```
$ gem sources --add https://gems.ruby-china.org/ 
$ gem sources --remove https://rubygems.org/
```

### 切换源时提示 Error fetching

https://gems.ruby-china.org/ 中的 `https` 换成 `http`


### 运行 `jekyll serve` 时的 `GBK` 问题

在此之前先运行 `chcp 65001` 命令
