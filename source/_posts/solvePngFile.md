---
title: Png文件压缩浅析
cover: /images/suofei1.png
tags: 
	- Png
	- Android
---

最近在写一些安卓的项目，但是需要对一些png文件进行压缩，但是tinyPng需要付费，以及调用api速度和便捷性并没有本地压缩快，所以来瞅瞅有没有合适的，虽然最后还是没有很好的方案，但还是增长了自己的见识。

<b>首先感谢掘金大佬的文章，非常的详细，受益匪浅[https://juejin.cn/post/6844904013557661709](https://juejin.cn/post/6844904013557661709)</b>

## png文件解析

众所周知PNG是一种无损的图像存储格式，比jpg的图像多存储了很多数据，比如透明度。当然知道只知道这些并不够。

兵欲善其事，必先利其器。要压缩Png我们首先要弄清楚png里面究竟是怎么回事，二进制文件通常主要有两部分，文件信息部分和文件数据部分，文件信息部分包含了png文件的各种信息，比如图片大小，颜色类型，位深度等信息。

信息数据包含的信息数据，这里可以看一下[png文件格式规范](http://www.libpng.org/pub/png/spec/1.2/PNG-Chunks.html),这里面对png中存在的chunk都有很详细的说明解释，有精力的话还可以看看[png白皮书](https://www.w3.org/TR/2003/REC-PNG-20031110/)。


## 文件压缩解析
一般来说文件压缩有几种方式，一个是调色板，通过固定的颜色进行压缩，一个是颜色量化，合并相似颜色，还有清除多余信息，调整同样可以减少png文件的大小。

### 调色板
这个是png本身存在的一种存储方式，png颜色存储有6种颜色存储类型，在IHDR块中第10个字节位置存储，有六种颜色类型，分别为01-06。
这里分别是颜色类型为03和06存储方式的Png文件。

<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/solvePng/solvePng1.png" alt="颜色模式为6(RGBA真彩色模式)">
	<span>颜色模式为6(RGBA真彩色模式)的png文件</span>
</div>
<br />


<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/solvePng/solvePng2.png" alt="存储颜色模式为3(调色板模式)的png文件">
	<span>存储颜色模式为3(调色板模式)的png文件</span>
</div>
<br />

调色板模式为03，一般png文件的颜色类型是06即包含透明色的RGB真彩色模式，前面一个字节可以看到是08,这个代表的是每个颜色所占的位数，08即为一个颜色占8位，一个字节大小。

调色板模式会生成一个固定的调色板来存储颜色，同时生成一个透明色的调色板来存储颜色的透明度，在图像数据中用一个字节来存放颜色在调色板中的索引位置，根据索引来生成png图像。

调色板的标识符为PLTE，其中存储的透明字符标识符为tRNS,透明数值存储块的索引下标对应调色板颜色的索引下标，tRNS中透明度占1个字节，对于没有透明度的颜色，即对不透明颜色不进行存储。


<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/solvePng/solvePng3.png" >
	<span>PLTE调色板标识位</span>
</div>
<br />

<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/solvePng/solvePng4.png">
	<span>tRNS透明度标识位</span>
</div>
<br />

调色板模式由于存储图像数据仅为1个字节，所以调色板模式下只能存储00-FF也就是256种颜色，所以这种压缩方式属于有损压缩，压缩的质量取决于我们的颜色量化算法，量化算法越好，那么我们的图像质量也就越好。这就涉及到png图片压缩的核心了。

### 颜色量化

颜色量化是指将相似颜色合并为一个颜色，在256色的调色板中就是需要将颜色合理的分布为256块来进行存储，量化算法越优秀，就代表颜色损失越小，这也是目前各种png压缩的核心，工具和工具间压缩的质量大小差异主要由此决定的，颜色量化压缩算法有[中位数切割法](https://en.wikipedia.org/wiki/Median_cut)，[八叉树聚类算法](https://en.wikipedia.org/wiki/Octree)，还有[Kohonen神经网络](https://en.wikipedia.org/wiki/Self-organizing_map)来实现颜色量化，Median Cut量化算法等等，很多工具都采用多种算法混合来进行颜色量化，比如在[pngquant](https://pngquant.org/)中使用使用Median Cut量化算法的修改版本来进行颜色量化，使用类似于梯度下降的过程来调整直方图以及对表现欠佳的颜色，多次重复“中位数剪切”，来进一步改善颜色，使用Voronoi迭代（K均值）校正颜色，从而保证局部最优的调色板。

### 清除多余信息

在PNG中也有一些信息对于图片影响不大，或者有一些多余信息，清除这些信息也能进一步减小Png图片的大小，比如使用photoshop切图时留下的软件信息等，根据[基于移动设备的PNG图片优化实现](https://www.cnki.com.cn/Article/CJFDTotal-DNZS200827087.htm),目前png中有四个关键数据块，在03(调色板模式)中，应该有五个数据块保留，分别为IHDR(文件头数据块),PLTE(调色板数据块),tRNS(透明颜色数据块,如果需要透明颜色需保留),IDAT(图像数据块),IEND(图像结束数据块),其余的数据块都可以去除。

### 颜色存储压缩

png的数据一般有多种压缩方式，比如基础的压缩方式采用LZ77压缩，可以采用压缩率更高的压缩方式，但是会存在风险，有的图像识别软件可能不包含其它压缩方式的解码方法，在某些地方可能需要手动解码。

## 压缩工具推荐

目前的压缩工具推荐两个，[pngquant](https://pngquant.org/)和[tinypng](https://tinypng.com/),根据测试，其它的压缩工具多多少少不是很好用。

其中tinypng效果最好，但是每月仅有500个调用量，可以使用试用模式无限调用，但是速度比较慢，而且不稳定并拥有限制。

pngquant可以本地使用，没有网络I/O速度还是可以的，转换5MB左右的图片耗时6S左右，产出的图像大小比tinypng要大10%左右，效果比tinypng略差，但是也没有差很多。