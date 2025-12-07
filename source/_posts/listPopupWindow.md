---
title: ListPopupWindow(PopupWindow)屏幕自适应无法取消解决方案
cover: /images/ri_chang4.jpg
tags: 
	- PopupWindow
	- ListPopupWindow
	- Android
---

最近在解决一个需求，要求在输入框下面弹出一个下拉框，一个并不很难的需求，但是在适配的时候出现了问题。

![](/images/meme/-657807745b263120.jpg)

### 技术选取

这里选取了ListPopupWindow,后面大佬都说这个比较坑，但是再坑也要比我这种小菜鸡写的好吧，大不了填坑嘛。

实现很简单，new一个ListPopupWindow，做一些初始化设置，然后塞一个ArrayAdapter塞些数据就完事了，当我以为这一切都被我轻松搞定之后测试就来了(⊙_⊙)?

### 坑出现了

刚才测试问我发生甚么事了，我说怎么回事，他给我发了几张截图，我一看！嗷！原来是弹出来下拉框把输入框挡住了，debug跟一下看看是什么情况，结果是这个ListPopupWindow不讲武德自适应调整了位置，我想随便找一下参数设置把这个自适应关掉，没想到它还不是随便能关掉的，愣是找不到关闭这个自适应的参数，好家伙，这只能啃源码了啊。

![](/images/meme/5ade912b608f1d3fa1ede998836f9d39.jpg)

### 代码排查

经过排查发现自适应页面的功能在`popupWindow`里面，它在进行展示的时候执行了，大家了解`PopupWindow`的话就会知道我们可以通过`showAsDropDown`来展示悬浮窗口，`listPopupWindow`也是这样调用的，自适应的代码就是在执行`showAsDropDown`的时候进行自适应的。

在下面的代码片段中`popupWindow`开始寻找锚点控件的坐标来确定弹出窗口的坐标位置，自适应的代码也是在这一步执行的。
<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/popupWindow/pic1.png">
	<span>寻找展示弹出窗口的绘制坐标</span>
</div>
<br />

我们再往下找，可以找到对坐标进行适应兼容的代码，分别对纵坐标和横坐标进行了自适应操作。
<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/popupWindow/pic2.png">
	<span>对坐标进行页面适配的代码位置</span>
</div>
<br />

但是这里还是没有到达遮挡我们控件代码的位置，在这里对`PopupWindow`的`mOverlapAnchor`参数，也就是是否遮挡锚点控件参数作用的地方，但是这里如果想要让这个参数起作用有一个限制条件。

锚点控件周围的大小要能够包含我们的弹出窗口，这里是调整纵坐标位置的，所以当锚点控件上下区域的高度大于弹出窗口的高度时才会通过这个参数漏出我们的锚点控件，同时会判断弹出窗口应该在锚点控件的上面还是下面展示。

如果当前可视区域的高度不够大要怎么办呢，这里就会执行`positionDisplayVertical`方法来对弹出窗口进行调整，在这里也可以找到我们的遮挡我们锚点控件的罪魁祸首。

<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/popupWindow/pic3.png">
	<span>弹出窗口空间适配方案代码</span>
</div>
<br />

在这里代码对弹出窗口的高度/宽度以及位置进行控制，这里我们看的主要是纵向适配的代码，所以这里就是对窗口高度和纵向位置进行控制的代码。

这段代码中先判断了当前弹出窗口的底部是否被遮盖无法显示，如果被遮盖那么就会调整弹出窗口的高度，保证弹出窗口的底部不被遮盖。

其次会开始判断弹出窗口的顶部是否被遮盖，如果被遮盖那么就会减少弹出窗口的高度，保证目前的窗口顶部不被覆盖。

这样调整下来可以保证弹出窗口不被覆盖，在整个屏幕中展示，如果我只是需要一个选择框没有任何问题，但是如果我要在选择框里面进行输入的话就会很有问题了，它会遮住我的输入框。


<div style="text-align: center;color: grey;font-size: 14px;">
	<img src="/images/popupWindow/pic4.png">
	<span>遮挡锚点控件的代码</span>
</div>
<br />

### 解决方案

我们已经知道究竟是谁在捣乱了，那么就要想一个办法让它不去挡住我们的锚点控件，首先，我们不可能调offset,因为这个已经在里面计算过了，那么我们好像可以调整高度，让弹出窗口的高度小于控件周围空间的高度那不就会让`mOverlapAnchor`参数生效了嘛，就不会被挡住了啊。

所以我们就可以先一步计算我们的空余空间大小，然后调整ListPopupWindow或者popupWindow的高度，这样就可以在保证锚点控件暴露的同时无缝兼容了，经过查找`PopupWindow`会在`layout`变化的时候进行兼容，那么我们就需要在外面加一个布局变化监听，抢在`PopupWindow`前面先进行高度调整，抢`PopupWindow`的路，让它没办法搞事情。大概就是这个思路，具体代码步骤流程如下
```java
void main(){
	// 定义popupWindow
	ListPopupWindow listPop = new ListPopupWindow(this);
	
	//定义适配器
	adapter = new ArrayAdapter<>(this, R.layout.your_item_layout, data);
	
	// 添加适配器
	listPop.setAdapter(adapter);
	// 设置锚点View
	listPop.setAnchorView(AnchorView);
	
	// 获取全局view
	View mainScreen = getWindow().getDecorView();
	// 定义布局变化触发事件
	ViewTreeObserver.OnGlobalLayoutListener globalLayoutListener = () -> {
	        if (!ExCommonUtil.isEmpty(mListPop) && mListPop.isShowing()){
	            refreshPopupWindow();// 更新popupWindow样式
	        }
	    };
	
	// 添加全局监听
	mainScreen.getViewTreeObserver().addOnGlobalFocusChangeListener(globalLayoutListener);
}

// 设置刷新事件

void refreshPopupWindow(){
	// 获取可视区域大小
	Rect rect = new Rect();
    getWindow().getDecorView().getWindowVisibleDisplayFrame(rect);
	// 获取锚点视图位置用来计算可使用区域大小
    int[] anchorLocation = new int[2];
    AnchorView.getLocationOnScreen(anchorLocation);
	// 可用高度 = 可视区域高度 - 锚点坐标高度 - 锚点视图高度 - listPopupWindow设置的位移
    int surplusHeight = rect.bottom - anchorLocation[1] - anchorLocation.getHeight() - listPop.getVerticalOffset();
	// 设置弹窗高度为剩余可用高度	
	listPop.setHeight(surplusHeight)
}

```

![](/images/meme/867862428576CC1A74F02898A287C95A552B28A95.jpg)