---
layout: post
Title: Linux usually used cmds
Date: 2013-03-08
tags: [Linux, Command]
---

alsamixer: 音量调节

### Show dd progress

```
# dd if=/dev/zero of=/tmp/foo &
# while pgrep ^dd; do kill -USR1 `pgrep ^dd`; sleep 10; done
```
Ref:
http://adam8157.info/blog/2011/05/show-dd-progress
http://www.commandlinefu.com/commands/view/4011/check-the-status-of-dd-in-progress

### burn DVD

You can use `brasero` to burn CD/DVD
