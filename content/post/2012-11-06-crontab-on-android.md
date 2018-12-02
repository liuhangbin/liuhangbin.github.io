---
layout: post
Title: How to use crontab on android
Date: 2012-11-06
tags: [Android]
---

`Note: the method below is not work on Nexus7 because N7 is not support init.d`

Install busybox(you need root permission) and sshd service

```shell
# mount -o remount,rw /
# vi init.rc
service daily_poweroff /system/etc/init.d/daily_poweroff.sh
    user root
    group root
    oneshot
# mount -o remount,rw /system
# mkdir /system/etc/init.d
# vi /system/etc/init.d/daily_poweroff.sh
---
#!/system/bin/sh
# link /system/bin to /bin
mount -o remount,rw /
ln -s /system/bin /

# Add /system/etc/passwd
mount -o remount,rw /system
echo "root:x:0:0:root:/root:/bin/sh" > /system/etc/passwd

# make crontab dir
mkdir -p /var/spool/cron/crontabs

# poweroff everyday at 00:30
echo "30 0 * * * /system/bin/reboot -p now" > /var/spool/cron/crontabs/root
---

# remount / and /system
mount -o remount,ro /
mount -o remount,ro /system

# start crond
crond -L /var/spool/cron/crontabs/crond.log

# chmod 750 /system/etc/init.d/daily_poweroff.sh    // don't know if 777 is ok

# mount -o remount,ro /
# mount -o remount,ro /system
```

Reference:
[cron on android](http://www.imoseyon.com/2011/02/cron-on-android-is-awesome.html)

[How to run scripts on
boot](http://android.stackexchange.com/questions/6558/how-can-i-run-a-script-on-boot)


How to enable init.d
------

`Tested on N7, still not work :(`

```shell
# mount -o remount,rw /system
# ls /system/etc/install-recovery.sh
-r-xr--r--    1 root     root           537 Aug  1  2008 install-recovery.sh
# chmod 755 /system/etc/install-recovery.sh
# vi /etc/install-recovery.sh

# init.d support
run-parts /system/etc/init.d

# ls /etc/init.d/       // this is to make sure init.d folder is exist
daily_poweroff.sh
# chmod -R 777 /system/etc/init.d

# vi /system/bin/sysint

#!/system/bin/sh

# init.d support
export PATH=/sbin:/system/sbin:/system/bin:/system/xbin
/system/bin/logwrapper run-parts /system/etc/init.d

# chmod 755 /system/bin/sysint
# chown 0.2000 /system/bin/sysint
```
