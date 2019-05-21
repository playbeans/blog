---
layout: post
title:  "arch 整理"
date:   2019-05-13 09:31:01 +0800
categories: linux
tag: arch
---

* content
{:toc}

### 添加超级管理员

1.首先添加用户名称
```
# useradd -m -g users -s /bin/bash  {username}
# passwd {username}

```

2.添加超级用户的权限
vim /etc/sudoers 如果没有这个文件就pacman -Syu sudo,添加第二行即可
```
root  ALL=(ALL)   ALL
{username}  ALL=(ALL)   ALL
```

### 添加arch源

1.官方源地址
```
http://mirror.bit.edu.cn/archlinux/ - 北京理工大学
http://mirrors.aliyun.com/archlinux/ - 阿里巴巴
http://mirrors.163.com/archlinux/ - 网易
```

2.arch源  demo
```
archlinux
How to use
Edit file '/etc/pacman.d/mirrorlist' and add lines as below to top of the file:

Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch
```

3.arch-cn源 vim /etc/pacman.conf 
之后安装archlinuxcn-keyring
```
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = http://mirrors.163.com/archlinux-cn/$arch
```

4.安装yay
```
pacman -S yay
```

### 安装桌面环境
1.安装xfce4
```
sudo pacman -S xorg xfce4 xfce4-goodies lightdm lightdm-gtk-greeter xorg-xinit xterm
```
2.lightdm 是桌面登录管理器,需要设置成开机启动
```
sudo systemctl enable lightdm.service
```

### 安装搜狗输入法
1.安装输入法的包
```
pacman -S fcitx fcitx-sougoupinyin  fcitx-im fcitx-fbterm kcm-fcitx fcitx-configtool fcitx-gtk2 fcitx-gtk3 fcitx-qt4 fcitx-qt5
```
2.配置~/.xprofile 没有就新建
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```
### 添加zsh源

1.安装zsh
```
pacman -S zsh zsh-completions
```

2.替换系统默认shel
```
chsh -s /bin/zsh
```
3.安装oh-my-zsh
```
#官网上的方法法，需要安装wget或者curl
$ sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

#方法二：当然也可以通过git下载 
$ git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

4.配置主题
vim ～/.oh-my-zsh/themes 修改之后执行 source ~/.zshrc
```
ZSH_THEME="ys"
```
### dock
1.安装需要自动启动  vim~/.config/autostart/或者~/.config/xfce4/xinitrc or ~/Desktop/Autostart 添加cairo-dock &
```
pacman -S  cairo-dock
```
### conky

