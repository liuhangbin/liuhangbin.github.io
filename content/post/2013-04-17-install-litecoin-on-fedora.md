---
layout: post
Title: How to install litecoin on fedora
Date: 2013-04-17
tags: [Fedora]
---

Download litecoin source code from github.

Install depend packages.
1) yum install -y libdb-cxx-devel boost-thread boost-filesystem boost-system
2) wget http://www.openssl.org/source/openssl-1.0.1e.tar.gz
3) tar zxf openssl-1.0.1e.tar.gz; cd openssl-1.0.1e/
2) Build openssl, cause fedora default openssl doesnt support ec.h
	1) ./Configure linux-x86_64 --prefix=/usr \
	--openssldir=%{_sysconfdir}/pki/tls zlib enable-camellia enable-seed \
	enable-tlsext enable-rfc3779  enable-cms enable-md2 no-mdc2 no-rc5 \
	--with-krb5-flavor=MIT --with-krb5-dir=/usr shared
1) make depend
2) vim crypto/Makefile , add -fPIC to CFLAG
3) make && make isntall

Install litecoin demon
---
3) cd litecoin/src, modify makefile.unix, change boost_thread to boost_thread-mt
4) `make -j4 -f makefile.unix USE_UPNP=`, 4 is you cpu
core number

Install litecoin-qt
1) `$ sudo yum install -y qt-devel miniupnpc-devel`
1) `$ qmake-qt4 BOOST_LIB_SUFFIX=-mt USE_UPNP=1`
2) `$ make`
