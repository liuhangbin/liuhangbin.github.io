---
layout: post
Title: How to use mutt
Date: 2013-03-08
tags: [Linux, mutt]
---

Referfenc :
http://leeon.me/a/mutt-muttrc-chinese-configuration-note
https://wiki.archlinux.org/index.php/Mutt_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E5.AE.89.E8.A3.85

http://xhc.me/wp-content/uploads/mutt/manual_1.5.19_zh.html
http://www.gentoo.org/doc/zh_cn/guide-to-mutt.xml

OfflineIMAP Usage:

https://wiki.archlinux.org/index.php/OfflineIMAP


Gmail的OfflineIMAP XOAUTH2認證
1. Gmail網頁版啓用IMAP
https://mail.google.com中Settings->Forwarding and POP/IMAP，開啓IMAP
Access，When a message is marked as deleted and expunged from the last visible
IMAP folder:最好選擇默認的Archive the
message，被刪除的郵件在網頁版中會丟失所有標籤，但仍能在“[Gmail]/All
Mail”中找到。
    訪問[Google Developers Console](https://console.developers.google.com/apis/credentials)
    選擇或新建一個project
    訪問https://console.developers.google.com/apis/credentials，點擊Create
        credentials->OAuth client ID，type選擇other，記錄client ID與client secret

2. OfflineIMAP XOAUTH2配置
[Repository Ray-Remote]
type = Gmail
ssl = yes
sslcacertfile = /etc/ssl/certs/ca-certificates.crt
remoteuser = youremail@example.com
auth_mechanisms = XOAUTH2
oauth2_client_id = xxx.apps.googleusercontent.com
oauth2_client_secret = xxx
oauth2_refresh_token = xxx

3. 默認配置文件https://github.com/OfflineIMAP/offlineimap/blob/master/offlineimap.conf有詳細的描述。
```
cd /tmp
git clone https://github.com/google/gmail-oauth2-tools
cd gmail-oauth2-tools
# 獲取refresh token，填入~/.offlineimaprc的oauth2_refresh_token
# 這一步需要藉由各種代理途徑，比如proxychains等
python2 python/oauth2.py --generate_oauth2_token --client_id=xxx --client_secret=xxx
```

REF: https://maskray.me/blog/2016-02-12-gmail-offlineimap-xoauth2
