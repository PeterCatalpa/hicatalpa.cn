---
title: 使用Cloudflared Tunnel实现内网穿透详细教程
tags: [教程,cloudflared,网络,内网穿透,超详细]
published: 2023-08-19
category: 教程
draft: false
description: "使用Cloudflared Tunnel实现免费的内网穿透"
---
# 准备
1.电脑
2.域名
3.脑子
# 写在前面
## 什么是内网穿透
内网穿透是一个非常重要的东西，它能让我们本地的一个端口开放到公网，让公网的其他人来访问。如果没有内网穿透，那么这个端口只能在私网（可能就是你家的局域网）里被访问到。举个例子，我开了一台Minecraft服务器，想让我的好朋友来玩，这时如果没有内网穿透（除非你有公网ip），那么你的好朋友甚至都连接不上你的服务器。
## 为什么选择Cloudflared Tunnel
众所周知，内网穿透国内有很多，比如Sakura Frp、OpenFrp、花生壳内网穿透，但是这些内网穿透不是要实名认证，就是要money，显然，这些都是对我们不太友好的。并且Cloudflared Tunnel有着更多在安全性上的服务。于是在众多的内网穿透服务商中，我选择了Cloudflared Tunnel。
## Cloudflared Tunnel的一些问题
首先，免费版的Cloudflared Tunnel服务器实在国外的，所以访问速度会比较慢，同时尽管有TCP的选项，但是似乎是不可用的。除此之外，UDP的协议也是不支持的，这样一来，Cloudflared Tunnel或许对于部分没有公网ip的服主不太友好。不过Cloudflared Tunnel整一个web项目也是可以的。
# 使用教程
## 注册Cloudflare账号（如果有账号，请跳过此步骤）
首先，打开[Cloudflare官网](https://www.cloudflare.com)。
![](/cloudflared-tunnel//1.png)
点击注册，这时网页会跳转到注册界面。
![](/cloudflared-tunnel//2.png)
语言可能是英文的。可以点击右上角的English，选择简体中文。
![](/cloudflared-tunnel//3.png)
填入自己的电子邮件，并自定义一个密码。“我愿意偶尔收到有关 Cloudflare 产品、服务和活动的电子邮件更新和特别优惠。”的格子可以勾上，也可以不勾。
![](/cloudflared-tunnel//4.png)
当出现类似下面的界面时，说明Cloudflare账户注册成功了。
![](/cloudflared-tunnel//5.png)
注册完之后，记得验证邮箱，否则无法使用Cloudflared Tunnel。
## 将自己的域名添加到Cloudflare
在购买域名的域名服务商那里把第一个NameServer改成arch.ns.cloudflare.com，第二个改成nina.ns.cloudflare.com。由于域名服务商过多，这里就不做演示了。
登录Cloudflare，点击“开始使用”。
![](/cloudflared-tunnel//6.png)
输入自己的域名，点击“继续”。
![](/cloudflared-tunnel//7.png)
选择Free计划。
![](/cloudflared-tunnel//8.png)
点击“继续”。
![](/cloudflared-tunnel//9.png)
后面照着提示去做就可以了。
## 下载Cloudflare可执行文件
### 方法一
打开https://github.com/cloudflare/cloudflared/releases/ ，可以看到这样的页面。
![](/cloudflared-tunnel//10.png)
找到合适自己电脑的版本，我的电脑是64位的，所以选择cloudflared-windows-amd64.exe的版本。
### 方法二
如果连接不上的话，就从下面的链接下载。
[64位](https://petercatalpa.lanzoue.com/i06Xy15waecf)（密码7fru）。[32位](https://petercatalpa.lanzoue.com/i5WB815waj7a )（密码：51ed）
## 配置
### 授权域名
把下载下来的文件名改为cloudflared.exe，并在此目录下按下shift和鼠标右键，选择“在终端中打开”，输入如下命令。
~~~
cloudflared tunnel login
#Windows输入这个：
#./cloudflared.exe tunnel login
~~~
这时浏览器会跳出登录窗口。
![](/cloudflared-tunnel//11.png)
点击你要使用的域名，点击授权。
完成后，会有这样的窗口。
![](/cloudflared-tunnel//12.png)
打开终端，也会显示登录成功。
![](/cloudflared-tunnel//13.png)
### 创建隧道
在终端中输入以下命令，创建隧道。
~~~
cloudflared tunnel create [名字]
#Windows输入这个：
#./cloudflared.exe tunnel create [名字]
~~~
名字是可以自定义的，但是不能重复。如果想要查看已经创建的隧道的话，可以输入以下命令。
~~~
cloudflared tunnel list
#Windows输入这个：
#./cloudflared.exe tunnel list
~~~
如果要删除，可以输入以下命令删除。
~~~
cloudflared tunnel delete [名字]
#Windows输入这个：
#./cloudflared.exe tunnel delete [名字]
~~~
### 配置路由
这一步是把隧道绑定到自己的域名上。
~~~
cloudflared tunnel route dns [名字或者 UUID] [想要绑定到的域名或其二级域名]
#Windows输入这个：
#./cloudflared.exe tunnel route dns [名字或者 UUID] [想要绑定到的域名或其二级域名]
~~~
### 启动隧道
#### http/https服务
~~~
cloudflared tunnel --name [隧道名称] --url http://[站点地址]
#Windows输入这个：
#cloudflared tunnel --name [隧道名称] --url http://[站点地址]
~~~
#### TCP服务
~~~
cloudflared tunnel --name [隧道名称] --url tcp://[服务地址]
#Windows输入这个：
#cloudflared tunnel --name [隧道名称] --url tcp://[服务地址]
~~~
运行任务大概就是这样。
![](/cloudflared-tunnel//14.png)
### 配置开机自启（可选）
如果想要让这个服务在电脑开机时自动启动，我们可以在cloudflared.exe的目录下创建一个bat文件，内容是
~~~
cd [cloudflared.exe所在的路径]
cloudflared tunnel --name [隧道名称] --url http://[站点地址] 或 cloudflared tunnel --name [隧道名称] --url tcp://[服务地址]
#Windows输入这个：
#cd [cloudflared.exe所在的路径]
#./cloudflared.exe tunnel --name [隧道名称] --url http://[站点地址] 或 ./cloudflared.exe tunnel --name [隧道名称] --url tcp://[服务地址]
~~~
保存后，右键，创建这个bat文件的快捷方式，在地址栏中输入shell:startup，在跳转到的目录中把快捷方式复制进去就可以了。
重启电脑，隧道就自动启动了！
# 后记
弄完这些，我们便把Cloudflared Tunnel搞好了，~~如果你够懒~~，可以把所有的项目都开机自启，便达到了“开机，就是开了所有”的境界。