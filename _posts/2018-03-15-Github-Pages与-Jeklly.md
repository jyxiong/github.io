---
layout: post
title: GitHub Pages 与 Jekyll
key: 20180315
tags: Test
---

> [GitHub Pages](https://pages.github.com/) 是一个静态网站托管服务，可直接从GitHub托管页面。    
> [Jekyll](https://jekyllrb.com/)是一个静态站点生成器，它会根据网页源码生成静态文件。  
> 使用 GitHub Pages 和 Jekyll，可以自由(**free**)且免费(**free**)地制作网站

##  GitHub Pages
1. 注册一个 [GitHub](https://github.com/) 账号
2. 创建基于用户名的repository(仓库)：[jyxiong.github.io](https://github.com/jyxiong/jyxiong.github.io)
3. 拉取现有模板到仓库中，如[Jekyll Themes](http://jekyllthemes.org/)
4. 根据模板提示修改配置文件_config.yml

## Ruby
> Jekyll 需要安装 Ruby  

Windows下直接下载集成好的[RubyInstaller](https://rubyinstaller.org/downloads/)来进行安装。注意选择正确的版本。  

## RubyGems
> 用 RubyGems 管理 Ruby 包  

RubyGems 是一个 Ruby 的包管理系统，类似于 Anaconda 与 Python 的关系。下载[RubyGems](https://rubygems.org/pages/download)压缩包，解压到本地任意位置
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
在浏览器打开 http://localhost:4000/ 即可在本地运行网站。

解决gem install jekyll 不能运行的问题 https://gems.ruby-china.org/
切换源
$ gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
$ gem sources -l
**确保只有 gems.ruby-china.org**

在切换到新的https://gems.ruby-china.org的源时，还是有可能会出现这个提示Error fetching，这个情况有两个可能：
(1)是因为系统是Windows的缘故，你可以把https://gems.ruby-china.org/中的https换成http，我就是这样成功更换源的。这个问题在ruby的社区里有人已经提到了。
(2)如果是其他系统，则可能是SSL证书没更新，更新SSL 证书后解决。另外有一点要注意的是Windows上相关的ruby操作命令请尽量在Windows CMD环境下执行。

GBK 问题
解决办法是在运行服务器前先运行chcp 65001命令，即可解决。
