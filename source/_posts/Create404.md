---
title: 在部署在git上使用hexo生成的博客中添加404页面
cover: /images/yuban2.jpg
tags: 
	- hexo
	- 404page
---
`hexo`可以在`github`上部署时设置404页面，错误页面将跳转到404页面
### 注意404页面只有在部署到github上时访问才可以跳转，本地无法跳转

有两种方式创建404页面
## 1.在`themes`文件夹下的主题中添加404页面
进入`/themes/当前主题/source`中添加`404.html`,这种方法可以自定义404页面的主题样式，但是需要自己编写404.html
这里提供腾讯的404页面
``` html
<!DOCTYPE HTML>
<html>
<head>
  <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="robots" content="all" />
  <meta name="robots" content="index,follow"/>
</head>
<body>

<script type="text/javascript" src="http://www.qq.com/404/search_children.js" charset="utf-8" homePageUrl="your site url " homePageName="回到我的主页"></script>

</body>
</html>
```
## 2.在`source`文件夹下添加404页面
进入根目录的`source`文件夹下，新建`404.md`文件即可，这种方法比较方便，可以直接根据当前的主题来生成404页面，但是不够自由