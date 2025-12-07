---
title: python使用import注意点
cover: /images/jiabaili.png
tags: 
	- Python
	- import
---

## &emsp;&emsp;萌新在python中使用import导包中需要注意的地方
#### &emsp;&emsp;最近在使用一些库的时候出现了这样的现象，当只使用`import`导入包的时候是不能直接使用其中函数的，只有当`from import`时才可以,所以特意研究了一下以前一直没有注意过的导包机制。
&emsp;&emsp;python中使用`import`可以导入很多东西，这次主要研究其中的包和模块，模块是指单个`py`文件而包是指具有多个模块或者子包的文件夹，由于本人一直习惯用import不怎么喜欢加from,所以东窗事发突然爆炸，就研究了一下导包。

#### 这里需要注意的是导入要使用的函数时最好使用`from import`。

&emsp;为什么要这样呢，因为导入包名其实是<b>无法直接使用包内的子包和模块的</b>，这点很重要，这也就是为什么有的包只用import时会报错的原因。

&emsp;很多接触python时间不长或者没怎么研究过导包的童鞋都会认为把一个包import后,里面的模块和子包就都能用了，就算不能用直接from import就可以，但是并没有探究过为什么会这样。

&emsp;python在只使用`import`导入包时只会将包中<b>一级目录下的文件</b>来引入命名空间，再下级的目录<b>并不会进行引入</b>，在同级中模块里的函数并不会检测，所以在使用模块里的函数时需要通过完整的路径调用。
#### 例如:
```
--package
  -aa.py
```
&emsp;现在aa.py中存在函数bb()

&emsp;我们调用`bb()`的时候如果使用的是`import package`不能够直接使用`package.bb()`来调用这个函数，必须使用`package.aa.bb()`来调用，这一点大家应该都能想明白，因为`python`在当前文件夹下只能检测到有`aa.py`这个文件，但是不可能再去查看`aa.py`里面有没有这个函数，如果挨个检测的话稍微大一点的包就要费好长时间，而且内存占用也会很多。

&emsp;<b>那么如果在这个包中具有一个子包呢</b>，这个时候子包就是个文件夹,`python`这里的源码我没有看,但通过猜想以及实验通常应该是在`import`一个包后只查找`.py`文件,例如我们的目录是这样的:
```
--package
  -aa.py
  -sonPackage
    __init__.py
    -cc.py
```
&emsp;这个时候如果你只是使用了`import package`这个时候如果想要调用`cc.py`的fun函数直接使用`package.sonPackage.cc.fun()`是会报错的，报错内容是找不到`sonPackage`这里是由于sonPackage是一个文件夹，并不是`.py`文件，所以编译器并没有把它引入到命名空间里。

&emsp;但是这样的话要怎么使用`cc.py`呢，这里有两种方式，一个是使用`import package.sonPackage`,还有一个是使用`from package import sonPackage`,这两种方式都可以让程序运行的时候把包里面的东西引入到命名空间。

&emsp;但是有一些库用的比较少童鞋或者一直跟着大佬代码导包的童鞋就有一些问题，为啥我看好多包直接就能用啊，根本没写过这么一大堆，这个问题的秘密就在于`__init__.py`这个东西了，当你进行导包的时候首先python就会去执行`__init__().py`,很多包都是在`__init__.py`中把常用的函数或者子包预先导入来方便使用的，这样就会出现我们可以直接使用子包和函数的情况，比如目前我在用的sklearn中其中一个子包的__init__.py中是这么预先导入的:
![code](/images/pythonImport.png)
&emsp;可以看到在其中已经导入了一些子包或者函数之类的东西，在这里是sklearn文件夹中的样子可以看到`_check_build`在代码中已经被导入了。
![code](/images/pythonImport2.png)
&emsp;所以这也就是为什么在导入一些包的时候我们可以直接使用其不同文件中的函数

&emsp;当然，在引用子包的时候子包中的`__init__.py`引入了相应的函数才能直接使用，不然你还是要通过其中模块来使用函数，这里是sklearn一个子包中的`__init__.py`,这里显示了它把函数都已经提前导入了。
![code](/images/pythonImport3.png)

&emsp;可能有人注意到了第一张图有一个名称为`__all__`的个列表，这个东西是用来定义当你`from package import*`时提供的模块和子包，如果没有这个变量当你`from package import *`只会自动导入<strong>当前一级目录下的模块，子包是不会导入的</strong>

&emsp;由于`__init__.py`在导包时就会执行，里面你也可以放一些花里胡哨的函数来直接用，但是就会很乱ヽ(ー_ー)ノ。

<strong>知识引用来自于知乎大佬[模块&包---import时发生的那些事](https://zhuanlan.zhihu.com/p/30836117)</strong>
以及来自大佬博客的[Python导入系统中粗心的陷阱](http://python-notes.curiousefficiency.org/en/latest/python_concepts/import_traps.html)
