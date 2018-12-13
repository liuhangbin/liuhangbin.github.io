---
layout: post
title: accept Invalid argument
date: 2015-03-18
description:
category:
Tags: []
---

accept: Invalid argument
---

int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);


Some times we got "accept: Invalid argument" error when write a socket
program. There are two kinds of faults which may case this issue:

1. Didn't call bind or listen on sockfd.
2. Didn't initialize the addr or addrlen. addrlen must be a positive value.

