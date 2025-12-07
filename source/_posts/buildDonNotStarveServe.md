---
title: 饥荒联机版(Don't Starve Together)搭建私服步骤整理
cover: /images/xiaohei1.png
tags: 
	- 饥荒联机版
	- linux
---

看到腾讯云有在打折，正好阿里云要过期了，续费非常之贵，于是就买了一个轻量级的腾讯云服务器，三年150的样子吧，1c2g网速还比阿里云快<b>1M</b>的带宽hhhhhhh。

但是用来干点啥还没想好，不过库里面的饥荒吃灰许久，可以拿出来被虐一虐，那就搭一个饥荒服务器吧。

<b>这里参考了CSDN大佬的文章，非常详细[https://blog.csdn.net/zhang41228/article/details/103106298](https://blog.csdn.net/zhang41228/article/details/103106298)</b>

## 1.配置担忧

这个看你需求吧，反正我这边目前<b>1c2g</b>(即1核2G内存)的还够用，地图的尺寸是<b>大</b>,听说开地下的话内存会不太够用，所以我就开的小地下，搭好之后玩起来完全没啥问题，就是内存占用确实不低。

<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/dontStarveTogether/pic1.png" alt="内存占用">
	<span>可以看到打开服务之后内存从四百多飙到了将近1.6个G</span>
</div>
<br />

我这边服务器最大人数设定的是4个，具体能不能搞下来等我凑够人了先吧，不过两个人玩起来还是可以的。

## 2.买买买服务器！！！
[阿里云](https://www.aliyun.com/minisite/goods?userCode=aoxio1km)

[百度云](https://cloud.baidu.com/campaign/bccdiscount/index.html)

[腾讯云](https://curl.qcloud.com/ExLYu2TZ)

[华为云](https://activity.huaweicloud.com/discount_area_v5/index.html)

哪个便宜买哪个,买的时候服务器设置成centos7以上的linux系统即可并且在安全组中打开10999和10998端口，这俩分别运行的是饥荒地图的服务和饥荒地下的服务。

以我目前的腾讯云为例直接进入控制台

![](/images/dontStarveTogether/pic2.png)

找到图中的防火墙，也可能叫做安全组，添加规则，端口设置为10999和10998即可

![](/images/dontStarveTogether/pic3.png)

## 3.开始搞事情

可以在网页控制台远程登录服务器开始操作，可能体验会比较不爽，或者直接用[xshell](https://www.netsarang.com/zh/xshell/)怎么用自己查查就会了。

以下的步骤分别为

### 下载基础环境
``` shell
sudo yum update	

sudo yum -y install glibc.i686 libstdc++.i686 libcurl4-gnutls-dev.i686 libcurl.i686 screen
```

### 安装steam的命令行工具
``` shell
cd /home && mkdir steamcmd && cd steamcmd

wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz

tar -xvzf steamcmd_linux.tar.gz
```

### 通过steam命令行下载饥荒服务端，默认设置为`/home/dstserver`想换地方就换吧，我是懒得换了。
```
./steamcmd/steamcmd.sh

login anonymous

force_install_dir /home/dstserver

app_update 343050 validate

```
大佬说有个坑，但是我装的时候好像没有碰到，应该是修好了?

踩坑的姿势要标准啊

要是出现`libcurl-gnutls.so.4`类似的错误试试下面的代码
```
ln -s /usr/lib/libcurl.so.4 /home/dstserver/bin/lib32/libcurl-gnutls.so.4
```
测试安装是否正常，出现YOUR Server Will Not Start就是正常了。
```
cd /home/dstserver/bin

echo "./dontstarve_dedicated_server_nullrenderer -console -persistent_storage_root /home/dstsave -conf_dir dst -cluster World1 -shard Master" > master_start.sh

echo "./dontstarve_dedicated_server_nullrenderer -console -persistent_storage_root /home/dstsave -conf_dir dst -cluster World1 -shard Caves" > cave_start.sh

chmod +x master_start.sh cave_start.sh
```

### 配置服务端设置
打开你电脑上的饥荒

新建一个多人房间，我这边设置的权限是公共，加密码，然后在上面设置好你要设置的东西，要加的mod，返回首页点击游戏数据即可，在里面前缀为Cluster的文件夹就是你生成的东西了


### klei生成token用来注册服务器

我们要来到[klei](https://accounts.klei.com/login?goto=https://accounts.klei.com/account/game/list)去注册服务器，没有登饥荒的直接进网站用steam或者什么乱七八糟的东西登录

有开饥荒的在首页账户信息里面直接进去就行，具体操作可以康康这个[https://forums.kleientertainment.com/forums/topic/64441-dedicated-server-quick-setup-guide-linux/](https://forums.kleientertainment.com/forums/topic/64441-dedicated-server-quick-setup-guide-linux/)

### 配置服务器以及你的小mod

快！！！把你生成的token直接塞到cluster_token.txt里面，保存。

然后打开你的cacaves或者master文件夹，找到`modoverrides.lua`用记事本打开。

在Cluster_1的目录里面新建一个记事本文件，名字叫做`dedicated_server_mods_setup.lua`

![](/images/dontStarveTogether/pic4.png)

把下面这玩意粘贴到刚才新建的记事本里面，把里面类似于`ServerModSetup("375850593")`东西里面的数字换成`modoverrides.lua`里面的mod编号，这样你的mod就可以被下载到了

```
--There are two functions that will install mods, ServerModSetup and ServerModCollectionSetup. Put the calls to the functions in this file and they will be executed on boot.

--ServerModSetup takes a string of a specific mod's Workshop id. It will download and install the mod to your mod directory on boot.
    --The Workshop id can be found at the end of the url to the mod's Workshop page.
    --Example: http://steamcommunity.com/sharedfiles/filedetails/?id=350811795
    --ServerModSetup("350811795")

--ServerModCollectionSetup takes a string of a specific mod's Workshop id. It will download all the mods in the collection and install them to the mod directory on boot.
    --The Workshop id can be found at the end of the url to the collection's Workshop page.
    --Example: http://steamcommunity.com/sharedfiles/filedetails/?id=379114180
    --ServerModCollectionSetup("379114180")

ServerModSetup("375850593")
ServerModSetup("375859599")
ServerModSetup("378160973")
ServerModSetup("463952377")
ServerModSetup("786556008")

--ServerModCollectionSetup("id")
```

<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/dontStarveTogether/pic5.png" alt="对照图">
	<span>大概就保持这个规则改一改</span>
</div>
<br />

我们搞定了这一堆东西后，然后你可以用各种方法把这一坨东西挪到你服务器上的那个`/home/dstsave/dst`里面,如果你之前开服务器测试的话里面应该会有一个`World1`的文件

看我嚣张的眼神，在这个位置，保证你在这个文件夹里面，执行我们伟大的`rm -rf *`,duang一下里面的东西就没有了，然后把咱们刚才整的东西扔进去就好了。

或者你比较害怕，可以找找工具比如xftp啥的把文件复制进去覆盖或者删除。

<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/dontStarveTogether/pic6.png" alt="">
	<span>千万确保自己在的文件夹没问题，不然你就要上演删库跑路了</span>
</div>
<br />

### 启动服务器
好了，到了我们激动人心的时候了，我们在经过乱七八糟的生成，修改，删除，覆盖之后终于可以开始运行我们的服务器了，输入以下命令运行主地图

``` shell
cd /home/dstserver/bin

screen -S master

./master_start.sh
```

按下`Ctrl+A`然后再按下`Ctrl+D`让它去后台搞事情去，咱们继续开地下

``` shell
screen -S caves

./cave_start.sh
```

同样的手法，把他扔后台，顺便检查一下他们过的好不好

``` shell
netstat -nlp |grep :10999

netstat -nlp |grep :10998
```

如果有显示数值表示他们过的很好，你可以到饥荒上去搜你的服务器撒欢去了，反正我是一遍就好了hhhhhhhh

<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/dontStarveTogether/pic7.png" alt="">
	<span>很明显他们过的不错</span>
</div>
<br />

要是哪天你闲这群玩意太占内存要删掉咋办，直接`kill`掉！！！
```
kill -9 6999

kill -9 7364
```
注意这里的数值对应刚才搜出来的值。


