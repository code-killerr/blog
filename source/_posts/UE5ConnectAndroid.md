---
title: UE5设置链接quest2(Android)出现Unable to locate local Android SDK location.
cover: /images/xiaohei2.png
tags: 
	- UE5
	- Android
---
最近正在追寻自己究竟要干什么，在复盘了过往经历后，仍然有一个东西萦绕在心头，以前可能不是很清晰，但是过来这么久仍然还在，那肯定就是我要干的东西了。
这里因为正好有设备，并且基于未来考虑，准备在VR方向进发，直接上UE5康康吧，当头的问题就是在链接打开`SetupAndroid.bat`时出现了问题

我们这边在报错为
```
Unable to locate local Android SDK location. Did you run Android Studio after installing?
```

这里我猜测是由于我的sdk没有在默认位置导致的，因为我C盘实在没地方了，但方法总是人想出来的嘛，咱们来扣一下这个bat

![](/images/UE5Connect/pic1.png)

这个是我们报错的位置，它先会去我们的C盘应用数据的位置找SDK，也就是我们安装`Android studio`时候默认安装SDK的位置

但是不是说了嘛，为了省空间我挪了一个地方，那么接下来会直接在环境变量找`ANDROID_HOME`，这玩意是啥啊，我感觉我从来没有设置过。

翻了一圈果然没有，安排上，打开环境变量新建`ANDROID_HOME`

![](/images/UE5Connect/pic2.png)

再次打开SetupAndroid.bat窗口一闪而过，发生甚么事情了，又给我报了一个奇怪的错误。

![](/images/UE5Connect/pic3.png)

咋这玩意还和我Chrome给磕上了，果断再查一下到底哪里有问题。

![](/images/UE5Connect/pic4.png)

在这里我打印了一下`USERPATH`这个是我的系统环境变量，里面出现了Chrome设置的环境变量，我就懵了逼了，这玩意咋还和我Chrome有关系了。

不管了，直接删，反正也不是啥厉害东西。

再次运行，这次应该是ok了吧。

![](/images/UE5Connect/pic5.png)

然后继续报错，好像说我.net框架版本太低了?人家要3.1，给了个[网址](https://dotnet.microsoft.com/download/dotnet?utm_source=getdotnetcorecli&utm_medium=referral)让我去下它,这要求可真多啊，整个最新版下总可以了吧。

![](/images/UE5Connect/pic6.png)

好了，我们成功了吧？？？？

![](/images/UE5Connect/pic7.png)

看起来可以了，就要开始漫长的等待着色器的加载了，这玩意怎么这么慢啊！！！！

![](/images/UE5Connect/pic8.png)