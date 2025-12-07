---
title: CSS布局
cover: /images/suofei3.png
tags: 
	- CSS
---

# Css布局

## 布局相关技术

### 1. 常规流
#### 常规流中的排版方式
#### (1)行级
只包含行级元素的块级容器会创建IFC(Inline Formatting Context)
###### IFC排版规则
- 盒子在一行内水平摆放
- 一行放不下时换行显示
- text-align决定一行内盒子的水平对齐
- vertical-aligan决定一个盒子在行内的垂直对齐
- 避开浮动(float)元素

<b>ps:</b>根元素，浮动和绝对定位元素会脱离常规流，其它元素不会脱离常规流
流中子集盒子应全为行级或全为块级，如果混合出现会被匿名盒子包起来。
#### (2)块级
在某些情况下容器会创建BFC(Block Formatting Context)
- 根元素
- 浮动,绝对定位,inline-block
- Flex子项和Grid子项
- overflow值不是visible时
###### BFC排版规则
- 盒子从上到下摆放
- 垂直margin合并
- BFC内盒子的margin不会与外面的合并
- BFC不会和浮动元素重叠

#### (3)Flexible box
[https://www.runoob.com/w3cnote/flex-grammar.html](https://www.runoob.com/w3cnote/flex-grammar.html)

可以使用属性来控制上下文内子集盒子的流向，摆放顺序，盒子宽度和高度，水平和垂直方向的对齐，是否折行等。

###### 使用方法
`display:flex` 使元素生成一个块级的Flex容器

`display:inline-flex` 使元素生成一个行级的Flex容器

###### flex 属性设置
&nbsp;&nbsp; 1. flex-direction

```css
flex-direction:row /* 从左到右 */

flex-direction:row-reverse /* 从右到左 */

flex-direction: column /* 从上到下 */

flex-direction:column-reverse /* 从下到上 */
```

&nbsp;&nbsp; 2. flex-wrap

```css
flex-wrap: nowrap /*不换行*/

flex-wrap: wrap /*换行*/

```

&nbsp;&nbsp; 3. flexibility
flexibility 可以设置子项的弹性(相当于百分比布局，完全利用空间并且避免元素出现溢出)
`flex-grow` 设置伸展能力

`flex-shrink` 设置收缩能力

`flex-basis` 没有伸展或收缩时的基础长度

<b>ps:</b>宽度优先级 flex-basis>width>content

快速设置: `flex: flex-frow flex-shrink flex-basis`

###### flex对齐

对齐分为<b>主轴</b>和<b>侧轴</b>

![](/images/cssImage1.png)

<b>主轴对齐属性</b>

```css
justift-content: flex-start /*对齐主轴开始侧顺次排列*/
justift-content: flex-end /*对齐主轴结束侧顺次排列*/
justift-content: center /*于主轴方向居中排列*/
justift-content: space-between /*各行之间留有空白*/
justift-content: space-around /*各行前后都有空白*/
justift-content: flex-enevly /*均匀排列，间隔相等*/

```

<b>侧轴对齐属性</b>
```css
align-items: flex-start /*同上*/
align-items: flex-end /*..*/
align-items: center /..*/
align-items: stretch /*所有元素为容器高度*/
align-items: baseline /*元素baseline对齐*/
```

align-self 设置单一元素侧轴对齐方式

align-content 设置整体在侧轴上的对齐方式

order 调整顺序，盒子将按照order从小到大排序

#### (4)Grid布局

可以将容器划分为网格进行二维布局。

###### 使用方式
`display:grid`

###### 划分网格
设置网格宽度`grid-template-columns: colums1 colums2 ....`

设置网格高度`grid-template-rows: row1 row2 row3.....`


<b>ps:</b> fr为平分，xfr表示平分为几份

###### 指定区域

```css
grid-row-start:1;
grid-column-start:1;
grid-row-end:3;
grid-column-end:3;
```

分别指明开始的两条边和结束的两条边

快捷方式`grid-area:1/1/3/3`

![](/images/cssImage2.png)


设置高度时可以给线命名用来划分区域
```css

grid-template-columns:
[left] 100px [center] 1fr [right]; /*左边线，中线，右边线，总共横行划为3个区域*/

grid-template-rows:
[top]
1fr
[middle]
1fr
[bottom];

/*从上到下划分为三个区域，总共三条线*/

grid-template-areas: "c0r0 c1r0" 
                     "c0r1 c1r1"
/* 快速命名*/
```
###### 设置间隔

 grid-row-gap //行间距
 grid-column-gap //列间距

#### 对齐设置

 justify-items //行对齐
 
 align-items //列对齐

 align-self //单个元素垂直对齐

 justify-self //单个元素水平对齐

 align-content //整个网格垂直对齐

 justify-content //整个网格水平对齐


#### (5)表格布局

<b>tips:</b>
表格中列宽不设置将自动根据内容分配

设置`table-layout:fixed`将使表格按照首行宽度计算，和下方内容无关

当总宽度超出表格宽度时将按照宽度比例分配宽度，不溢出

单元格边框需要给td,th同时加上边框

`border-collapse: collapse`将合并相邻边框

使用`display:table`可以使其它标签变为table




### 2. 浮动(float)

通过浮动最初为了实现文字环绕效果，float包括right和left

#### 浮动对布局影响

![](/images/cssImage3.png)

#### 清除浮动对于布局的影响

(1) clear: 清除浮动，具有right,left,both来清除左,右或者全部浮动
(2) 创建BFC来包裹浮动元素，设置`overflow:hidden`来创建BFC
(3) 使用伪元素 
```css
:after(){
	content: '';
	displat:block;
	clear:both;
}
```

当使用BFC时可以使float元素被其它元素包围而不是覆盖其它元素



### 3. 绝对定位

#### position属性

![](/images/cssImage4.png)


#### (1)position: relative

![](/images/cssImage5.png)

#### (2)position: absolute
![](/images/cssImage6.png)

#### (3)position: fixed
![](/images/cssImage7.png)

### 4.堆叠层级

<b>z-index</b>可指定定位元素的层级，即relative,absolute,fixed才可设置

![](/images/cssImage8.png)

<b>tips:</b>
同一个堆叠上下文中的元素才可以比较z-index,即不同父级的子元素无法比较堆叠，以父级元素堆叠为准

#### 堆叠上下文创建

![](/images/cssImage9.png)

#### 堆叠上下文元素绘制顺序
![](/images/cssImage10.png)
