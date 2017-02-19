 Deepin（基于Debian的Linux系统）安装Shadowsocks
http://blog.csdn.net/wan_yanyan528/article/details/52572786
标签： debiandeepinlinuxshadowsock
2016-09-18 11:14 507人阅读 评论(0) 收藏 举报
 分类： 大杂烩  Linux（2）  
版权声明：本文为博主原创文章，未经博主允许不得转载。

目录(?)[+]
感谢万能的Github，感谢幕后无私奉献的前辈们！ 
首先借这个机会介绍下Deepin系统吧，这是武汉深之度科技有限公司基于debian开发的一套自主知识产权的操作系统，也算是打个广告吧，感谢无数前辈在自主操作系统上的努力付出。这里是深度官网，感兴趣的话不妨体验一下。 
Deepin系统上安装Shadowsocks需要安装从源码进行编译，这里是github源码： 
shadowsocks-qt5 
直接安装这个会发现提示缺少依赖包libqtshadowsocks-dev，所以需要先安装这个包，万幸的是github也有这个包的源码： 
libQtShadowsocks 
以下是详细安装步骤：

1、安装libqtshadowsocks-dev

① 安装依赖包

sudo apt-get install qt5-qmake qtbase5-dev libbotan1.10-dev
1
1
② 从github克隆libqtshadowsocks-dev的源码

git clone https://github.com/shadowsocks/libQtShadowsocks.git
1
1
③ 进入libQtShadowsocks目录

cd libQtShadowsocks
1
1
执行命令：

dpkg-buildpackage -uc -us -b
1
1
如果g++命令未找到，先安装g++即可：

sudo apt-get install g++
1
1
如果以上命令执行成功，会在上级目录生成两个deb文件（版本号可能不同）： 
libqtshadowsocks-dev_1.9.0-1_amd64.deb 
libqtshadowsocks_1.9.0-1_amd64.deb 
④ 安装libqtshadowsocks-dev 
先安装libqtshadowsocks，再安装libqtshadowsocks-dev：

sudo dpkg -i libqtshadowsocks_1.9.0-1_amd64.deb
sudo dpkg -i libqtshadowsocks-dev_1.9.0-1_amd64.deb
1
2
1
2
2、安装shadowsocks-qt5

① 安装依赖包

sudo apt-get install qt5-qmake qtbase5-dev libqrencode-dev libqtshadowsocks-dev libappindicator-dev libzbar-dev libbotan1.10-dev
1
1
② 从github克隆libqtshadowsocks-dev的源码

git clone https://github.com/shadowsocks/shadowsocks-qt5.git
1
1
③ 进入shadowsocks-qt5目录

cd shadowsocks-qt5
1
1
执行命令：

dpkg-buildpackage -uc -us -b
1
1
如果以上命令执行成功，会在上级目录生成deb文件（版本号可能不同）： 
shadowsocks-qt5_2.7.0-1_amd64.deb 
④ 安装shadowsocks-qt5

sudo dpkg -i shadowsocks-qt5_2.7.0-1_amd64.deb
1
1
3、安装完成后的配置

安装完成后就可以在系统启动器中找到shadowsocks，安装成功后如下图： 
这里写图片描述
这里写图片描述 
安装完后连接代理服务器，发现google依然无法访问，这里需要浏览器插件支持才能访问，以chrome为例： 
chrome浏览器需要安装SwitchyOmega插件，插件可以从这里下载。 
下载完以后打开chrome的扩展程序，将其拖进扩展程序，chrome会自动完成安装。安装以后打开SwitchyOmega的设置页面，点击“导入/导出”—-“从备份文件恢复”来恢复PAC，备份文件可以从这里下载，也可以导入自己的备份文件。 
这里写图片描述
导入以后点击浏览器右上角SwitchyOmega的图标，选择“自动切换”，这时应该就能够访问google了。 
这里写图片描述 
浏览器会根据访问的页面自动判断是采用代理还是直接访问，但是对于一些不常用的网站，PAC文件可能没有该网站的列表，无法通过代理访问，这时可以点击SwitchyOmega图标，点击“添加条件”来手动设置该网站的访问方式。 
这里写图片描述 
这种方式只能针对特定的浏览器设置代理，如果要在终端使用，还需要另外设置，设置方法参考这篇博客的后半部分，我还没有测试过，测试以后再来补充。
