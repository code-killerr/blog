---
title: Vmware内部错误
cover: /images/xiaomai2.jpg
tags: 
	- Vmware
---
## 打开Vmware出现内部错误提示
当开启虚拟机时弹出窗口提示"内部错误"

查询发现是由于相关服务没有开启，需要进行手动开启

选择计算机右键打开菜单，选择管理
![](/images/VmwareError1.png)

点击进入后选择服务和应用程序进入，选择服务选项，在其中找到Vmware相关的服务开启

![](/images/VmwareError2.png)
再次进入Vmware启动虚拟机

![](/images/VmwareError3.png)

虚拟机启动成功