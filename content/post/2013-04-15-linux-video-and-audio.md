---
layout: post
Title: Some useful cmd for linux audio and video
Date: 2013-04-15
tags: [Linux, Audio, Video]
---

将视频里的音频提取出来的方法
---
1. Install mencoder
`sudo yum install -y mencoder`
2. reencode
`mencoder -oac MP3lame -ovc copy -of rawaudio $input_file -o $out_mp3_file`
3. use `mencoder -oac help` to see all support format


Cmus – music player / or Try mocp
---

cmus 是一款相当不错的控制台音乐播放器。它小巧快速，而又功能强大。cmus 支持
Ogg/Vorbis、MP3、FLAC、Musepack、WavPack、WMA、WAV、AAC、MP4 等格式，包含
Gapless 播放及 ReplayGain 支持，Vi风格的按键绑定，播放列表过滤，可定制配色
方案，UTF-8 支持等等。
