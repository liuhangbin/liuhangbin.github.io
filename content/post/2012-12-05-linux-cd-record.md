---
layout: post
Title: Linux CD Record
Date: 2012-12-05
tags: [Linux, Sometime_Use, CD]
---

如何刻录
---

linux下刻录光盘非常简单，只需在终端窗口输入以下命令即可： 

cdrecord -v speed=2 dev=0,0,0 cd.iso

speed是表明刻录速度的选项，可根据实际情况设置，但不要超过CD-R与刻录机的最大限速；dev是刻录设备的选项，可由 

cdrecord -scanbus 

命令获得，cd.iso是要刻录的影像文件，可根据实际情况加入路径。

如何制作ISO
---

linux中有一个mkisofs命令，功能非常强大，可以做资料光盘、系统光盘等，还可以做可启动光盘。 

mkisofs -r -o cd.iso -m temp burn/ 

是基本的使用方法，－o cd.iso 指定光盘映像文件名称，－m temp 排除temp目录或文件， burn/ 将该目录下的文件加入到映像中，更高级的使用请参照相关文档或man。 

光盘拷贝
---

像MS系统下的盘对盘刻录偶没有试过，但做成映像文件再刻录安全性更高，偶一直使用此法，这时就不需要再用mkisofs命令了，还有更简单的做法，使光盘信息一丝不漏，包括启动信息： 

dd if=/dev/cdrom of=cd.iso 

/dev/cdrom为光驱设备名称，一般为/dev/cdrom ，可根据实际情况给定；cd.iso就不需再说了。


REF
---

Reference : http://bbs.chinaunix.net/thread-2225787-1-1.html
