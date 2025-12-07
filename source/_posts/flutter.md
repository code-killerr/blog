---
title: Flutter框架搭建webApp
cover: /images/ri_chang1.jpg
tags: 
	- Flutter
	- Web
---
Flutter是Google的SDK，可通过单一代码库为移动，Web和桌面提供精美，快速的用户体验。对于应用开发非常方便，对于Andorid App支持原生开发，混合开发以及webApp开发，由于自己太菜，所以从最简单的webApp开发开始，查阅官方文档，开始搞事情ヽ(ー_ー)ノ
本次我们需要的环境为:
#### 1.Flutter SDK
#### 2.Chrome浏览器
#### 3.一个合适的IDE
## 1.Flutter安装
Flutter有多个`branch`,在`Flutter`文档中提供了安装稳定构建渠道的 `Flutter SDK`的`git`代码
``` git
git clone -b stable https://github.com/flutter/flutter.git
```
但我们这次打算整一下webApp所以需要合适的`Flutter`版本，在文档中同样有其它`branch`的下载代码
``` git
git clone -b beta https://github.com/flutter/flutter.git
```
当然，由于国内原因导致了下载速度可能过慢，这时候只要增加两个环境变量即可更改下载源
`添加PUB_HOSTED_URL值为https://pub.flutter-io.cn`
`添加FLUTTER_STORAGE_BASE_URL值为https://storage.flutter-io.cn`
这样就可以刷刷的下起来了┗( ▔, ▔ )┛
同样的，不想用`git`的小伙伴在`Flutter`文档中同样也有相关压缩包的下载链接，我先搬这了啊。
<div style = "width:100%;margin:auto;text-align:center">
	<a href = 'https://storage.flutter-io.cn/flutter_infra/releases/stable/windows/flutter_windows_v1.12.13+hotfix.8-stable.zip' >
		<span  style = "background-color:darkblue;color:white;padding:10px 20px 10px 20px">我是FlutterSdk压缩包哦</span>
	</a>
</div>
## 2.Flutter配置
下载完成后我们万里长征开始了第一步，接下来进行框架相关的配置:
1.复制`flutter`下的`bin`目录路径添加到环境变量`path`中，这一步是为了能够在命令行中执行flutter命令
2.打开`cmd`命令行输入以下命令来检查版本是否最新
``` shell
flutter upgrade
```
3.输入以下命令开启web支持
``` shell
 flutter config --enable-web
```
4.输入以下命令进行相关依赖检测
```
flutter doctor
```
检测结果中需要Flutter,Chrome,Connected device三个选项为正常，由于本次为web开发，并不强制要求具有android studio或者xcode
5.输入以下命令列出可用设备
```
flutter devices

```
返回的样式应该和下列返回值相近
```
Chrome     • chrome     • web-javascript • Google Chrome 78.0.3904.108
Web Server • web-server • web-javascript • Flutter Tools
```
## 3.项目建立
这些搞定后我们的环境就差不多了，下来就该去建立项目了，在需要建立项目的文件夹中使用cmd命令行输入以下命令即可创建项目
```
flutter create myapp
```
输入以下命令在chrome中部署应用,注意cmd命令行所在位置应该为项目文件所在位置
```
flutter run -d chrome
```