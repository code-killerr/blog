---
title: python爬虫总结
cover: /images/ri_chang3.jpg
tags: 
	- Python
	- spyider
	- 爬虫
	- requests
	- BeautifulSoup
---

## 使用python进行数据爬取总结

&emsp;&emsp;最近一直在使用python来进行数据的爬取，主要是由于工作需求，于是对如何使用python进行了研究，由于应届生嘛，会的东西可怜，唯一有点优势的就是学的快了。

&emsp;&emsp;为了学习嘛，从最基础的方向开始，不使用框架只使用基础库来进行代码编写。

### 1.对于库的选取
&emsp;&emsp;数据爬取这种东西在学校大概学过一些，所以对于网页的处理选取使用了bs4,网页访问在网上查了好多都是用的`requests`库，那么就用`requests`吧，真的可以说python太简单了，使用`requests`一行代码就能搞定与网页的链接，网络这种高大上的东西一直都觉得很复杂呢(T＿T)，好吧我承认上网络的时候我是飘过去的。
### 2.爬取过程以及遇到的问题
&emsp;&emsp;既然库选定了，那么就开始数据的爬取吧，主要流程很简单，综合来说就是使用`requests`进行网页的获取，然后使用`BeautifulSoup`来进行数据整理，从里面筛选出有用的数据.首先写下获取网页的代码。
```python
requests.get(url)
requests.post(url)
```
&emsp;&emsp;其中url填写要爬的网址就行了，真的很简单，get一般用来单纯的得到网页信息而post则用来对网页发送数据，然后网页才会给你返回你想要的东西，千万不要弄混了，不然会出现网页获取内容有问题的情况，我就干了好多次(/ω＼)，我知道我菜，不要打了o(╥﹏╥)o

&emsp;&emsp;同时胆小的我怕被网站封ip,或者防止无法通过某些网站的检测我还给请求加了个(headers)头，对，一个正常的头往往能掩饰一个不正经的人(｀＾´)ノ
```python
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36','Connection': 'close'}
```
&emsp;&emsp;好了，这个玩意就是我们的头的，有了头的我们就可以站着走路了，不用天天顶着一个python的头到处晃悠了，看见那个Chrome嘛，多大多耀眼!!!∑(ﾟДﾟノ)ノ

&emsp;&emsp;由于我是写了一段时间才发的文章，所有注意的一次写清楚吧，在进行爬取的时候还会出现这样的错误。
```python
Caused by SSLError(SSLError("bad handshake: SysCallError(-1, 'Unexpected EOF')",),)
```
&emsp;&emsp;这里可以设置`verify`为`false`来进行规避，但是关闭后会有很讨厌的`warnning`,使用
```python
requests.packages.urllib3.disable_warnings()
```
&emsp;&emsp;可以忽略掉`warnning`,到这里当然还没有结束，一个可怕但是又难以发现的问题出现了，使用`requests`时会出现假死的情况，需要设置`timeout`,当请求时间超过延迟时间时将断开连接并报`run timeout`错误。这个坑是真的坑，程序放在一边爬上几十个小时，回头一看假死了，实际上就爬了几个小时，真的太浪费了，没错，我干过这事，别打了别打了o(╥﹏╥)o，我知道我菜。

&emsp;当然还有最重要的一点就是异常处理，真的对于这种动不动就有错误的网络连接你不希望一个报错就让程序停止吧，年少不知加异常，爬取数据重连忙(Ｔ▽Ｔ)

&emsp;还有一个是设置重连次数，次数设置的越高，那么爬网页失败的几率越小，但是花费的时间也就越多,使用如下代码就可以进行次数的设置了
```python
requests.adapters.DEFAULT_RETRIES = 3
```

&emsp;&emsp;好了，我们的`requests`就处理完了，把这些东西综合了一下写出来了函数对进行处理
```python
#忽略警告
requests.packages.urllib3.disable_warnings()
#加个头
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36','Connection': 'close'
}
#断线重连次数
requests.adapters.DEFAULT_RETRIES = 3
def requestData(url,datas = None):
    global errorNum
    flag = False
    for i in range(0,3):
            try:
                if datas == None:
                    response = requests.get(url = url,headers=headers,verify=False,timeout = 60)
                else:
                    response = requests.post(url = url,headers=headers,data = datas,verify=False,timeout = 60)
                flag = True
                break
            except Exception as e:
                print(url+' is error!\n'+str(e)+'\n\n')
                flag = False
                time.sleep(10)
                continue
    if not flag:
        print(url + ' cant connect!!')
        return None
    return response

```
&emsp;&emsp;这里请允许我解释一下我干了点啥，由于经常爬的是国外的数据，所以当没连上时我又任性的进行了三次重连操作以保证获取到的数据量，好吧ヽ(ー_ー)ノ主要是因为后面使用了多线程爬虫，导致连接失败几率大增，所以使用这样的方式来减少同时对于相同网站的请求频率，反正后面也要说多线程，就简单谈一下我这么干的想法吧。

&emsp;&emsp;如果开启多线程，就像一条路突然涌进去了一大推车，那么造成的结果就是，这条路堵住了，大家都想同时走，谁都不让谁，那么怎么样才能不堵呢，就是让一部分的车先在边上停一停，把路让出来，减少短时间的车流量，这样不就通了嘛，所以遵循这个思路写出了这种请求方式。

&emsp;&emsp;这么设置的好处在于，如果你的网络很ok，对方服务器也没问题，那么一般不会出现错误，这个东西也用不上，因为你没错误正常过去请求就好了，但是如果突然出现网络波动，会出现一些线程请求成功了，但是一些线程请求失败了的情况，有这种情况的出现，那么说明网络出现了问题，为了减轻负担，最好的做法就是减少流量，减少流量的方式就是，失败的线程你晚点走，把道路先空出来让其它的线程先请求，这样同时进行请求的线程就变少，那么就算网络波动，仍然还是可以让我们一部分的线程顺畅的进行请求，后面没啥问题了，过了10秒错误的线程在重新进行请求，恢复正常的工作，这东西咋和车辆限号出行这么像呢。

&emsp;&emsp;当然如果路还是堵着的话，你们挤一挤让别的线程继续等呗，这样的话一般可以让请求数永远都适配网络环境，换句话说就是能让汽车的数量刚好和道路的宽度适配，每次过去的车都刚好是道路最大能够通行的车的数量。我废话好多啊(￣～￣;)，当然网络请求中应该是有这种机制的，但是我没有深挖这方面，就先用自己写的比较安心吧，当然这样会使错误数减少的另一个原因应该就是系统重连3次，我这再来三次，一波重连九次肯定能增加链接成功的概率啊。

&emsp;&emsp;当然这玩意缺点还是有的，比如一个线程老是抢不到上路的机会，然后彻底凉凉这样的。

&emsp;&emsp;`requests`进行请求搞定了，后面就是数据处理分析了，BeautifulSoup找东西不老简单了，先获取请求内容，然后根据标签找就完了
```python
response = requestData(pageUrl)
if response != None:
#整合页面获取文章url
	if response.status_code != 200:
        print(pageUrl+' statue_code error')
        continue
    soup = BeautifulSoup(response.text, "html.parser")
else:
    continue
#页面内容获取规则制定
result =soup.findAll(tag)
for element in result:
    element = element.find(name=tag, attrs={attrs :tagAttrs})

```
&emsp;&emsp;其中`soup = BeautifulSoup(response.text, "html.parser")`是将获取到的网页信息通过`BeautifulSoup()`将数据转换为文档树。

&emsp;&emsp;使用`soup.findAll(tag)`来对所有同名称的标签进行查找,注意这里返回的是一个列表，要使用`for`来遍历提取出其中的内容，进行内容筛取的时候可以使用`soup.find(name=tag, attrs={attrs :tagAttrs})`,这个函数中`tag`为需要寻找的标签，`attrs`为标签的属性，`find()`将会返回匹配的第一个元素。

&emsp;&emsp;当然这些不是太过于重要的内容，真正重要的是你能不能摸清楚你要爬取的网站其中的构造或者其中请求数据的方式，在这里主要有两种方式，第一种是最明显也是最好爬取的，采用`url`进行传参，这时候只需要更改url的内容就可以爬取数据。

&emsp;&emsp;还有一种稍微复杂的，使用`post`进行传参，这时候就需要使用`chrom`提供的开发者工具来检测网络，查看网页发送数据的格式，然后再模仿格式使用`post`方式来爬取数据。

&emsp;&emsp;当然还有的网站比较变态，喜欢采用一些奇奇怪怪的方式，比如我前面写的排除法获取页面，这个是真的让人懵逼。

&emsp;&emsp;在爬取数据的时候我一般是从目录开始爬取的，所以说我分成了两个部分第一部分是先爬取目录中的链接，第二部分是爬取链接中的网页内容，正常操作就是获取一页链接爬取一页内容这样的。
### 3.爬虫进阶
多线程，ip池，分布式没有多个设备不好实现
### 4.尝试方向，使用框架等