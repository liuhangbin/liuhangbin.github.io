---
layout: post
Title: How to install dropbox in RHEL6
Date: 2012-11-05
---

This tutorial will show you how to install and configure Dropbox in Red Hat
Enterprise Linux 6. Dropbox is a popular online storage service that has been
used by million people around the world. If you are running RHEL 6, this
tutorial below may help you to install Dropbox in RHEL 6.

Normal install
------

1. First, download the Dropbox rpm package from [here][1] or [here][2]
2. You will get a file name: nautilus-dropbox-0.6.8-1.fedora.i386.rpm
3. Then start installing Dropbox using command `# rpm -ivh nautilus-dropbox-*.rpm`
4. You can start Dropbox via Applications | Internet | Dropbox
5. On the first run, you will be notified to download and install Dropbox daemon.
    ![dropbox-red-hat](../pic/dropbox-red-hat.png)
6. Press OK to start download and install the daemon.
    ![dropbox-install-redhat](../pic/dropbox-install-redhat.png)
7. When finished, you can then configure and connect your Dropbox account.
    ![dropbox-login](../pic/dropbox-login.png)

Install via Proxy
------

As all we know GFW, our deamon install will fail in china if you use graphics.
Add in a terminal start Dropbox with the following command.
```
$ dropbox start
Starting Dropbox...
The Dropbox daemon is not installed!
Run "dropbox start -i" to install the daemon
```

This tells us that the Dropbox daemon needs to be installed... so run the
command as suggested.
```
$ dropbox start -i
```

This will start the installer and if you have a proxy in the way then this
process will throw you an error. What you need to do is specify the proxy
settings (including username and password. For some reason setting it via
Gnome (System->Preferences -> Network Proxy) does not seem to work and we need
to set the Environment variable http_proxy.
```
export http_proxy=http://username:password@proxy:8080
```

If you re-run the dropbox start -i command now the install should proceed as
required and you will have a nice working install of Dropbox running. Note
also that during installation it is possible to set the Proxy information that
will be used by Dropbox for everyday running so there is no need to put the
previously mentioned export command in your .bashrc file.

Run dropbox via Proxy
------

Dropbox uses the proxy settings from your operating system's Internet
configuration settings by default. You can change these settings as follows:

Using Dropbox Preferences
-----

Set proxy settings for Dropbox by manually entering them in the Dropbox
preferences panel:

1. Right-click on the Dropbox icon in the system tray
```
    ![dropbox-icon](../pic/dropbox-icon.png)
    The Dropbox icon in the system tray
```
2. Select `Preferences` from the Dropbox menu
3. Click on the `Network` tab
4. Fill in your proxy settings under the section appropriately labeled `Proxy Settings`

For our advanced users
-----

Dropbox will adhere to your system's http_proxy environment variable when
connecting to the internet on all distributions that support it.
example, in your terminal:
```
export http_proxy=http://username:password@proxy:8080
```

Reference:
[1]: https://www.dropbox.com/install?os=lnx
[2]: https://linux.dropbox.com/
[Dropbox install](http://gamblis.com/2011/07/16/how-to-install-dropbox-in-red-hat-enterprise-linux-6/)
[Dropbox help](https://www.dropbox.com/help/21/en)
[Dropbox wiki](http://www.dropboxwiki.com/Text_Based_Linux_Install)
[Install through proxy](http://gingerbreaddesign.co.uk/todd/2011/04/09/how-to-get-dropbox-on-linux-working-through-a-proxy/)
