---
layout: post
title:  "arch 整理"
date:   2019-05-21 09:31:01 +0800
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
vim /etc/sudoers 如果没有这个文件就pacman -Syu sudo,添加第二行即可，或者执行visudo
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
1.1安装xfce4（原先的最爱）
```
sudo pacman -S xorg xfce4 xfce4-goodies lightdm lightdm-gtk-greeter xorg-xinit xterm
```
1.2lightdm 是桌面登录管理器,需要设置成开机启动
```
sudo systemctl enable lightdm.service
```

2.1安装杂项
```
pacman -S xorg-server xorg-xinit dbus xf86-video-intel nvidia xf86-input-evdev xf86-input-synaptics
```
2.2安装cinnamon 和lightdm
```
pacman -S cinnamon lightdm-gtk-greeter accountsservice gvfs gvfs-mtp gvfs-afc faenza-icon-theme ntfs-3g exfat-utils xfce4-terminal
```
> cinnamon没有自带的term所以需要额外安装

2.3开机启动
```
sudo systemctl enable lightdm.service
```

### 安装搜狗输入法
1.安装输入法的包 fcitx-sougoupinyin or  fcitx-googlepinyin
```
pacman -S fcitx fcitx-configtool fcitx-sougoupinyin fcitx-cloudpinyin fcitx-gtk2 fcitx-gtk3 fcitx-im
```
2.配置~/.xprofile 没有就新建
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

### 安装字体
```
pacman -S ttf-dejavu wqy-zenhei adobe-source-code-pro-fonts
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
### vmware中的系统随着窗口的缩放进行缩放
```
1.点击虚拟机->vmware-tools安装，放到一个合适的位置
2.git clone https://github.com/rasa/vmware-tools-patches.git
3.cd vmware-tools-patches
4../patched-open-vm-tools.sh （不需要翻墙，时间比较久，如果失败删除vmware-tools-patches文件夹，继续执行）
```
#### sublime安装
```
1.1 sudo yay -S sublime-text-dev（不建议）
or
2.1 curl -O https://download.sublimetext.com/sublimehq-pub.gpg && sudo pacman-key --add sublimehq-pub.gpg && sudo pacman-key --lsign-key 8A8F901A && rm sublimehq-pub.gpg
2.2 echo -e "\n[sublime-text]\nServer = https://download.sublimetext.com/arch/stable/x86_64" | sudo tee -a /etc/pacman.conf
2.3 sudo pacman -Syu sublime-text
```
### dock
1.安装需要自动启动  vim~/.config/autostart/或者~/.config/xfce4/xinitrc or ~/Desktop/Autostart 添加cairo-dock &
```
pacman -S  cairo-dock
```
### conky


### 后记
```
2019.5.26 vmware15真的bug多，开始我居然不知道，以为是我自己的问题，重复安装了好几次，一安装桌面之后在重启，就卡死了，后来换了vmware14 屁事没有。果然软件还是不能调最新的使用。
```
