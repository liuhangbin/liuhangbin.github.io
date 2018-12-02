---
layout: post
Title: Linux环境下APE转MP3
Date: 2013-03-26
tags: [Linux, Music]
---

下载了一些ape的歌曲想传到google music,
可惜不支持，网上找ape转mp3的方法，mac那个项目打不开了，于是换这个了。

linux下做这种转换的步骤就是ape转wav，然后wav转mp3，最后利用cue文件分割mp3.

1. ape2wav
此过程需要ffmepg（没有的话sudo apt-get install ffmpeg, or yum install -y
ffmpeg)，命令如下：

ffmepg -i stefanie.ape temp.wav

转换过程比较慢。。

2.wav2mp3

需要lame(sudo apt-get install lame),转换命令如下：

lame -b 128 temp.wav temp.mp3

-b后面的128表示码率，网上下的mp3一般都是128的码率。可以自己调整

3.分割mp3.

由第二步得到的是一个整个专辑的mp3，所以需要利用cue文件（一般会和ape文件一起下载）进行分割：

        （1）转换cue文件的编码，需要enconv（sudo apt-get install enconv):

                             enconv stefanie.cue

               enconv比iconv好用多了，因为它会自动猜测文件编码并做相应的修正。如果不进行cue文件编码的转  换        的话，分割出的mp3文件名就是乱码的。

          （2）mp3文件的分割，需要mp3splt (sudo apt-get install mp3splt),命令如下：

                 mp3splt stefanie.mp3 -c  stefanie.cue

4.清理工作,删除过程中生成的中间文件：

                 rm temp.wav temp.mp3

5.大功告成，以上就把专辑stefanie.cue转换成了一首首的mp3文件。如果只想转化一首单曲形式的ape，做前两步就可以了。。。

偷懒技巧：
---
当然，懒人就要懒到底，写个脚本自动完成以上步骤就行了，哈哈，新建ape2mp3文件，写入以下内容：

#!/bin/sh
if [ $# -eq 2 ]
then
    echo "***********************************"
    echo "*****Step1转换ape为wav*****"
    echo "***********************************"
    ffmpeg -i "$1" .temp.wav
    echo "***********************************"
    echo "*****Step2转换wav为mp3*****"
    echo "***********************************"
    lame -b 256  .temp.wav .temp.mp3
    echo "************************************"
    echo "*****Step3切分mp3文件*****"
    echo "************************************"
    enconv "$2"
    mp3splt .temp.mp3 -c "$2"
    rm .temp.wav .temp.mp3
    echo "******转换完成！****"

else
    echo  "usage:ape2mp3 filename(ape) filename(cue)"
fi

写好之后保存，然后赋予执行权限：

chmod +x ape2mp3

偷懒进阶：
---
如果你有很多的ape要转的话，再写个for循环把要转的都敲进去吧

然后让脚本慢慢跑，自己睡觉去。。。。。

Reference: http://luzuoqing.blogbus.com/logs/50751162.html
