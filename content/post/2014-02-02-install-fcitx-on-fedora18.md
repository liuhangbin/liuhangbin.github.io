---
layout: post
Title: How to install fcitx on fedora 18
Date: 2014-02-02
tags: [im, fcitx, ibus]
---

1. 安装 fcitx 输入法及配置工具: `sudo yum install fcitx-pinyin fcitx-configtool`
2. 配置以允许使用 iBus 之外的输入法：`gsettings set org.gnome.settings-daemon.plugins.keyboard active false`
3. 在终端使用 $ im-chooser 或者利用输入法配置工具选择 fcitx（安装输入法选择： sudo yum install im-chooser）
4. 在输入法配置工具中注销或者利用终端注销 $ gnome-session-quit
5. 重新登入，即可用 Ctrl+Space 切换

PS:
1. 如果之前有安装ibux 并且设置了 Ctrl + Space
快捷键，那么切换到fcitx后会无法用快捷键切换输入法，这个是因为快捷键冲突导致的。方法是去system
setting - keyboard - shortcuts - Typing - Switch to next resource.
看你是否定义过相同的快捷键。 如果是的话就disable掉这个。
方法是点击快捷键，然后点回车backspace 就 disable 了。

Reference :
https://fcitx-im.org/wiki/FAQ/zh-cn#GNOME_3.6_.E5.8F.AF.E8.83.BD.E7.9A.84.E9.97.AE.E9.A2.98
