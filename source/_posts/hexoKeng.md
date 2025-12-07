---
title: 第一篇随意的开始(git+hexo部署踩坑)
cover: /images/yuban1.jpg
tags: 
	- hexo
	- github
---
博客终于弄好了，其中还是有几个坑的，所以总结一下以备不时之需
## git之坑
1.git主要问题在于ssh的设置，如果ssh设置有问题在最后一步时会报错
``` bash
ssh_exchange_identification: read: Connection reset by peer
```
使用
```bash
ssh -T git@github.com -v
```
来查看具体信息发现报错信息是
``` bash
key_load_public: No such file or directory
```
也就是告诉我在git上找不到我的公钥，分析一下应该是两种原因。
第一种网络有问题或者是git上的ssh还没有更新到数据库或者有延迟之类的，等一下或者换一个网络就好了。
我就是因为校园网把端口关了导致出现问题，一换热点立马好，坑爹学校阿里云连不上就算了连git都给我封
第二种是在git上录入的公钥有问题，使用
``` bash
ssh-keygen -E md5 -lf ~/.ssh/id_rsa.pub
```
对照输出的指纹和git上面的指纹是否一致，确保完全一样，使用
``` bash
ssh -T git@github.com
```
进行连接完成设置
## hexo之坑
1.hexo3.0后服务器单独的放了出去，所以在不装hexo服务器的情况下使用`hexo s`会报错所以我们在git bash中使用
``` bash
$ npm install hexo-server --save
```
来安装hexo服务器

2.主题文件直接下载在themes文件夹下，在站点根目录下(即themes所在位置)的_config.yml中更改主题

3.public文件夹中存储了生成的网页

4.主题的语言更改位置同样在站点根目录下的_config.yml中更改

5.博客的目录和布置在你下载的主题中的_config.yml中设置更改删除

6.文章内容写在source/_posts下

7.注意设置deploy时候在冒号后面加上空格，不然你hexo d后不会有任何反应