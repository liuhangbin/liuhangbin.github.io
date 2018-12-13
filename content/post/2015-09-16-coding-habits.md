---
layout: post
title: coding habits
date: 2015-09-16
description:
category:
Tags: []
---

Socket:

1. remember init(memset/bzero) client/server addr before use it. or the
scope_id may not init to 0 on some arch, which make sendto failed.
