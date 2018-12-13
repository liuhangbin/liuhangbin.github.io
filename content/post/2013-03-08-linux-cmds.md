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

### copy message to clipboard

cat doc.txt | xsel	# use middle mouse key or Control + Shift + Insert to paste
cat doc.txt | xsel -b	# use Control + v to paste

### File Manager

ranger

### yum

1. download package without install it
yum install yum-plugin-downloadonly
yum install --downloadonly --downloaddir=<directory> <package>

or

yum install yum-utils
yumdownloader <package>

2. create a local mirror of other repo

# yum install yum-utils createrepo
# reposync --gpgcheck -l --repoid=$repo_name  --download_path=/download/path
# cd /var/www/html/<download directory>
# createrepo -v /var/www/html/<download directory>/

to enable yum-security or groupinstall, add --downloadcomps
--download-metadata for reposync
