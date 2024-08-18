---
title: emby_jellyfin
date: 2024-05-31
---

# 解决 Docker 安装 Jellyfin 封面图和部分中文字幕变方块

1. 在容器中安装中文字体
```
apt update
apt install fonts-noto-cjk-extra
reboot
```

2. 重新生成封面

在封面图上选择“修改图片”，删除已有的封面图。然后在进入控制台-计划任务，触发“提取剧集图片”“扫描媒体库”，重新生成封面图。

3. 永久解决办法: [添加安装变量](https://github.com/linuxserver/docker-jellyfin/issues/161)

```
-e DOCKER_MODS=linuxserver/mods:universal-package-install
-e INSTALL_PACKAGES=fonts-noto-cjk-extra
```

# 
