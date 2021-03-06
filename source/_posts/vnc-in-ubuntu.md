---
title: vnc in ubuntu
categories:
  - coding
  - linux
tags:
  - vnc
  - linux
date: 2017-12-18 15:24:02
update: 2017-12-18 15:24:02
---

Ubuntu 16.04 LTS 安装VNC，在百度和google上面找了很多的教程，基本都不成功，一些问题好多人问，但就是没有回答。

最后搭建成功了的是Xfce桌面环境，unity/gnome一直是个残缺的样子（木有左边的菜单栏和右上角的时间，登出）
<!--more-->
### Xfce环境下的VNC

#### Step 1：安装桌面环境和VNC服务器

安装`Xfce`和`TightVNC`

> sudo apt install xfce4 xfce4-goodies tightvncserver

#### Step 2：配置VNC服务

首先备份并修改`~/.vnc/xstartup`文件的内容
> mv ~/.vnc/xstartup ~/.vnc/xstartup.bak

新的`~/.vnc/xstartup`文件内容为：
```
#!/bin/bash
xrdb $HOME/.Xresources
startxfce4 &
```

确保服务能正常运行，修改文件的权限（否则会报权限不够的错误）：
> sudo chmod +x ~/.vnc/xstartup

#### Step3: 启动服务器并测试连接

首先启动服务器：
>vncserver :1

**note**： 关闭服务器的命令为：`vncserver -kill :1`

控制台输出：
```
New 'X' desktop is your_server_name.com:1

Starting applications specified in /home/sammy/.vnc/xstartup
Log file is /home/sammy/.vnc/liniverse.com:1.log
```

客户端用realVNC viewer连接`server_ip_address:1`。

出现下图：

![vnc-ubuntu-xfce](/images/vnc_ubuntu_xfce.png)


#### Step4: 创建VNC服务

新建文件：
> sudo vim /etc/systemd/system/vncserver@.service

内容为:
```
[Unit]
Description=Start TightVNC server at startup
After=syslog.target network.target

[Service]
Type=forking
User=sammy
PAMName=login
PIDFile=/home/sammy/.vnc/%H:%i.pid
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1
ExecStart=/usr/bin/vncserver -depth 24 -geometry 1280x800 :%i
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target
```

让文件生效：
> sudo systemctl daemon-reload
> sudo systemctl enable vncserver@1.service

启动服务：
> sudo systemctl start vncserver@1


### gnome的vnc

与上述的差别有两个地方，一是安装相对的桌面环境，二是修改`~/.vnc/xstartup`文件。

当然安装的vnc服务可能也不同，例如`vnc4server`：
> sudo apt-get install vnc4server

#### 桌面环境

> sudo apt-get install ubuntu-desktop gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal

#### xstartup文件

```
#!/bin/sh

# Uncomment the following two lines for normal desktop:
# unset SESSION_MANAGER
# exec /etc/X11/xinit/xinitrc

[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
vncconfig -iconic &
x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
x-window-manager &

gnome-panel &
gnome-settings-daemon &
metacity &
nautilus &
```

**note**：好多google出来的xstartup文件配置都不能完美的解决本文最开始说的问题，所以就不一一列出来其他写法。

### 环境的删除

#### gnome的删除

>  sudo apt-get remove gnome-shell #卸载掉gnome-shell主程序
>  sudo apt-get remove gnome #卸载掉gnome
>  sudo apt-get autoremove #卸载不需要的依赖关系
>  sudo apt-get purge gnome #彻底卸载删除gnome的相关配置文件
>  sudo apt-get autoclean #清理安装gnome时候留下的缓存程序软件包
>  sudo apt-get clean

#### Xfce的删除

>  sudo apt-get -f install
>  sudo apt-get clean
>  sudo apt-get autoclean
>  sudo apt-get update
>  sudo apt-get purge xfce4
>  sudo apt autoremove

## 其他问题
1. 在VNC中Xfce4中Tab键失效的解决方法: http://blog.csdn.net/xuezhisdc/article/details/48662435
2. ubuntu下sublime无法输入中文的解决方法：https://www.jianshu.com/p/bf05fb3a4709
