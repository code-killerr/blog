---
title: quest2免路由激活(热点挂载VPN)以及无线串流操控电脑踩坑
cover: /images/suofei2.png
tags: 
	- Oculus
	- quest2
	- hotspot
---

<font size=5>最近安排了Quest2,但期间真的有很多问题，趟了很多坑，同时也get到了很多东西，在此分享记录一下，以备不时之需</font>

## 电脑挂载vpn选择

<font size=4>首先查看自己挂的梯子存不存在TAP_Windows Adapter类似的链接</font>

![](/images/quest2_img1.png)

<font size=4>如果没有，恭喜你进入了第一个坑，这里我们就需要更换相应的魔法客户端了一般来说小飞机，sstap,v2ray等都会有</font>

#### 当然，也有另一种情况，就是你是直接买的现成的vpn，不需要用结点，随开随用的那种，这种的话就有点难受了

<font size=4>首先你可以先查看你的魔法方式有没有相应的结点信息，例如</font>
<font size=4>
这种的
![](/images/quest2_img2.png)
或者这样的
![](/images/quest2_img2.png)

</font>
<font size=4>
类似的信息，如果找到了，看一下前缀是ss,还是ssr,或者是ssrt，根据这个前缀去找对应的其它客户端就好了^.^

但是如果没有找到，那就可能是被封装掉了，只能找一找结点了。
本篇并不推荐魔法(怕被拉去喝茶)(；´д｀)ゞ，这就需要自己寻找了
</font>
## 分享热点
<font size=4>
先打开热点共享，打开之后我们就能在网络连接中看到我们的热点了

![](/images/quest2_img6.png)

这里我们在TAP_Windows Adapter中右键选择属性

![](/images/quest2_img4.png)

找到共享，选中允许其他网络用户通过此计算机连接，跳出来窗口点确定之类的就行，网络连接选择开启热点后的连接，应该会有后缀*

![](/images/quest2_img5.png)

用手机或者平板之类的链接热点，试一下能不能魔法上网

如果不行的话，我们就要挂一下代理了，在这里

![](/images/quest2_img7.png)

设置开启代理服务器，地址按照图中即可，端口自己设置没有被占用的就行

在手机或者quest2上设置代理,

代理在wifi中选中相应wifi即可设置，选择手动

![](/images/quest2_img9.jpg)

填写代理信息，IP填写热点的ip地址即可，端口号输入电脑中设置的端口号

![](/images/quest2_img8.jpg)

热点的ip地址选中热点链接，右键点击状态-详细信息，即可查看

![](/images/quest2_img10.png)

这下应该就可以连接到魔法了，使用小飞机测试可以使用。sstap不需要代理即可使用

如果还是连不上，嫌麻烦，最傻瓜的方式是下一个connectify
[官网链接](https://www.connectify.me/zh-hans/)

打开之后是有免费版，免费版支持一次共享一小时，之后会断掉需要重开

![](/images/quest2_img11.png)

选择之前的TAP_Windows Adapter直接启动热点就可以了
</font>
### <font color="red">注意很重要的一点,激活quest2需要开启udp转发</font>
<font size=4>
quest2很容易被墙的dns污染，所以需要使用udp转发dns。

众所周知，网络请求= DNS域名解析 + TCP 连接建立 + HTTP数据传输

如果没有DNS解析就无法获得相应网站的ip,更不要说建立连接了，可以使用sstap以及v2ray,小飞机也是有的，不过电脑端没找到怎么设置

当然，如果下载软件到我们的quest2也是需要有udp转发的。

好了，大功告成，我们可以去激活我们的quest2了。ψ(*｀ー´)ψ
</font>
## 账号的坑
<font size=4>
facebook账号是个很让人恼火的东西，所以在用facebook账号注册时你需要，尽量使用真名，登录facebook的时候尽量使用一个固定位置结点。

如果经常换节点，facebook会把你封锁掉，操作不当的情况下，解锁是需要身份证的，尼玛给你身份证你用啥给我验证，合着收集身份证呢，可把我恶心坏了
</font>
## 免费无线串流电脑

### <font color="red">注意，本方法无法连接steamvr,只是可以操作电脑以及将电脑投屏到quest2 </font>
<font size=4>
首先打开我们oculus商店，搜索Immersed，这个是一个免费办公串流软件，可以将电脑串流至quest2,目测底层和vd差不多

打开后应该是下面这个样子

![](/images/quest2_img12.png)

然后电脑进入Immersed[官网](https://immersedvr.com)，输入quest2上的账号邮箱以及代码，下载pc端

打开之后应该是下面的样子

![](/images/quest2_img13.png)

关闭电脑和<b>quest2</b>上的所有魔法vpn,以及vpn,确认电脑和quest2连在一个wifi上

输入quest2中的代码就可以串流到我们的电脑了，相当于多了好多显示屏呢，因为immersed支持无显示屏分屏

今天你学废了吗(⊙_⊙)?

![](/images/quest2_img14.png)

<font size=4>





