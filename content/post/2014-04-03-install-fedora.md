---
layout: post
Title: How to install fedora 20 with mini install
Date: 2014-02-02
tags: [fedora, install, awesome]
---


### config git repo

```
# cat /etc/yum.repos.d/nay.repo
[nay]
name=Fedora $releasever - $basearch
baseurl=http://download.englab.nay.redhat.com/pub/fedora/linux/releases/20/Everything/x86_64/os/
enabled=1
gpgcheck=0
```

* Install X

```
yum -y --nogpgcheck install xorg-x11-server-Xorg xorg-x11-server-utils \
xorg-x11-utils xorg-x11-xinit xorg-x11-apps xorg-x11-drv-intel \
xorg-x11-drv-evdev xorg-x11-drv-keyboard dbus-x11
```

* Install desktop env

```
yum install -y awesome libnotify xsel sdcv slock scrot xrandr arandr ranger \
gtk2-engines  clearlooks-phenix-* tango-icon-theme gtk-chtheme \
dejavu-sans-mono-fonts wqy-zenhei-fonts \
gdb ctags cscope automake make gcc git xterm vim-enhanced wget \
firefox fcitx fcitx-cloudpinyin fcitx-configtool irssi screen offlineimap ntpdate cups \
unrar p7zip lftp vsftpd tigervnc tar alsa-utils vpnc
```
awesome: a light desktop
libnotify: send notify message
xsel: copy from Clipboard
sdcv: dictonary
slock: lock the screen
xrandr: config multi screen
arandr: gui for xrander
ranger: file manager
gtk2-engines clearlooks-phenix-* tango-icon-themea: themeas
gtk-chtheme: gui tools to change gkt cheme
pcmanfm: gui file manager
fcitx fcitx-cloudpinyin: input method
dejavu-sans-mono-fonts wqy-zenhei-fonts: fonts
alsa-utils: sound manager


### Install Work related
```
# wget http://download.lab.bos.redhat.com/beakerrepos/beaker-client-Fedora.repo -O /etc/yum.repos.d/beaker.repo
# yum install -y krb5-workstation conserver-client
```
krb5-workstation: for kinit
conserver-client: for console


* config awesome
```
$ cat .xinitrc
exec awesome
$ startx
```
