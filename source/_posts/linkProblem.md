---
title: 远程连接部分函数不受支持
cover: /images/kangna.jpg
tags: 
	- 服务器
	- 远程连接
---

买了一个服务器玩在使用mstsc命令进行连接时出现报错，远程连接部分函数不受支持。研究了一下发现这个是因为微软Win10更新导致实现远程连接操控服务器时出现如下报错信息
![](/images/link1.png)
搜索方案后发现拥有两种解决方式
## 1.修改注册表，适用于几乎所有windows10系统
#### (1)使用win+R打开运行框输入regedit进入系统注册表界面
#### (2)找到`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
#### （3）选中`System`右击弹出框中新建项`CredSSp`，选中`CredSSp`右键新建项`Parameters`，选中`Parameters`新建DWORD （32位）值（D）类型命名为`AllowEncryptionOracle`双击该项更改数据数值：
#### 如果为本地修改注册表：
0和1表示服务器必须是在注册表修补了 CredSSP 的情况下才可以连接
2表示服务器可以在没修补 CredSSP 的情况下连接
一般输入2即可
#### 如果为服务器端修改注册表:
0表示客户端必须在注册表修补了 CredSSP 的情况下才可以连接
1和2表示客户端注册表可以没修补 CredSSP 的情况下连接
一般选1或2即可
![](/images/link2.png)
## 2.修改本地组策略，针对专业版以上的win10系统 
#### (1)按下win+R显示运行框输入gpedit.msc打开本地策略组
#### (2)打开管理模板/系统/凭据分配 选择`Encryption Oracle Remediation`或者加密`Oracle`修正
#### (3)双击后选择已启用，保护级别或`Protection Level`选择易受攻击或`Vulnerable`
#### (4)点击确定，连接服务器，如果不行重启机器。
![](/images/link3.png)
