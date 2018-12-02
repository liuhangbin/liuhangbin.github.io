---
layout: post
Title: Linux DISPLAY set properly
Date: 2013-04-15
tags: [Linux, Display]
---

Some time we meet error like :
Error: Unable to initialize gtk, is DISPLAY set properly?

Then Try 

```
# export DISPLAY=:0.0
# export wxUSE_GUI=0
```

Referenct:
http://blog.csdn.net/jiangxinyu/article/details/1630713
