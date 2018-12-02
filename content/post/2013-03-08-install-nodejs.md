---
layout: post
Title: How to install Node.js
Date: 2013-03-08
tags: [Linux, JS]
---

Install Node.js
---
1. Install via tar ball
```
$ wget http://nodejs.org/dist/node-v0.4.12.tar.gz
$ tar xf node-v0.4.12.tar.gz
$ cd node-v0.4.12
$ ./configure
$ make
$ make instal l
``
1. Install via git
```
$ git clone git://github.com/joyent/node.git   
$ cd node
$ ./configure
$ make   
$ sudo make install
```

Install nmp (Node Packaged Modules)
---
1. use `curl http://npmjs.org/install.sh | sh`, but I didnt success
1. download it and install
```
$ wget http://npmjs.org/install.sh
$ sudo ln -s /usr/local/bin/node /usr/bin/node
$ chmod +x install.sh
$ sudo ./install.sh
```

Install node.js modules, like marked
---
1. directly install `nmp install marked`, this will install the module in
curent directory.
1. try `nmp install marked -g` ? should install globally
1. I tried with `nmp install marked/ -g`
