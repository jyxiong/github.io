首先你需要ruby来使用本地jekyll。Mac和Linux可以用Terminal配合yum或者brew这样的包管理器很方便的安装ruby。Windows下更是方便，可以直接中集成好的Ruby installer来进行安装，文章里的就是传送门。

安装完ruby，之后就是要安装RubyGems，gem是一个ruby的包管理系统，可以用gem很方便的在本地安装ruby应用。安装方法//在RubyGems官网上下载压缩包，解压到你的本地任意位置
//在Terminal中
cd yourpath to RubyGems //你解压的位置
ruby setup.rb

有了gem之后安装jekyll就很容易了，其实用过nodejs和npm的同学应该很熟悉这样的包安装，真是这个世界手残脑残们的救星。。。。。（楼主不自觉的摸了摸自己快残了的手） 安装jekyll，有了gem，直接在Terminal里面输入以下代码：
$ gem install jekyll

解决gem install jekyll 不能运行的问题 https://gems.ruby-china.org/
切换源
$ gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
$ gem sources -l
# 确保只有 gems.ruby-china.org

在切换到新的http://gems.ruby-china.org的源时，还是有可能会出现这个提示Error fetching，这个情况有两个可能：
(1)是因为系统是Windows的缘故，你可以把https://gems.ruby-china.org/中的https换成http，我就是这样成功更换源的。这个问题在ruby的社区里有人已经提到了。(2)如果是其他系统，则可能是SSL证书没更新，更新SSL 证书后解决。另外有一点要注意的是Windows上相关的ruby操作命令请尽量在Windows CMD环境下执行。

解决办法是在运行服务器前先运行chcp 65001命令，即可解决。
