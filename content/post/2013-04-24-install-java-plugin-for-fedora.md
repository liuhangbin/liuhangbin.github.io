---
layout: post
Title: How to install JAVA plugins for chrome/firefox in fedora18
Date: 2013-04-24
tags: [Install, Fedora, Java]
---

Step 1:
download Linux x64 RPM in http://www.java.com/en/download/manual.jsp?locale=en

Step 2:
open terminal and type the following
su -
cd /home/**username**/Downloads (replace **username** with yours)
rpm -ivh jre-7u11-linux-x64.rpm
you will see some few errors but its ok

Step 3:

updatedb
locate libnpjp2.so
copy the path of the libnpjp2.so file

Step 4a: (Firefox Users)
cd /home/user/.mozilla/plugins (replace user with your username)
ln -s path/of/libnpjp.so

Step 4b: (Chrome Users)
cd /opt/google/chrome/
mkdir plugins
cd plugins
ln -s path/of/libnpjp.so

Step 5:
close and run your browser. visit other java websites to check your java
installation. 

Bonus:
To open the java console, open a terminal (non-root account) then type
cd /usr/java/jre1.7.0_04/bin/
. ControlPanel

Reference:
http://foobaring.blogspot.com/2013/01/howto-install-java-plugins-in-google.html
