---
layout: post
Title: How to install fcitx on fedora 18
Date: 2014-02-02
tags: [im, fcitx, ibus]
---

1. 安装 fcitx 输入法及配置工具: `sudo yum install fcitx fcitx-pinyin fcitx-configtool`
2. 配置以允许使用 iBus 之外的输入法：`gsettings set org.gnome.settings-daemon.plugins.keyboard active false`
3. 在终端使用 $ im-chooser 或者利用输入法配置工具选择 fcitx（安装输入法选择： sudo yum install im-chooser）
或者用 imsettings-switch fcitx 直接切成 fcitx, 在 .bashrc 中加入
```
export  GTK_IM_MODULE=fcitx
export  QT_IM_MODULE=fcitx
export  XMODIFIERS="@im=fcitx"
```
1. alt + <F2>, r 重起 gnome

PS:
1. 如果之前有安装ibux 并且设置了 Ctrl + Space
快捷键，那么切换到fcitx后会无法用快捷键切换输入法，这个是因为快捷键冲突导致的。方法是去system
setting - keyboard - shortcuts - Typing - Switch to next resource.
看你是否定义过相同的快捷键。 如果是的话就disable掉这个。
方法是点击快捷键，然后点回车backspace 就 disable 了。


=== 安装完看不到选词界面
1. 运行 fcitx-configtool
2. 设置中有个添加输入法，下面的选项中有一个当前语言，把那个选项取消掉，
   往下拉就可以看到搜狗了！

Reference :
https://fcitx-im.org/wiki/FAQ/zh-cn#GNOME_3.6_.E5.8F.AF.E8.83.BD.E7.9A.84.E9.97.AE.E9.A2.98
