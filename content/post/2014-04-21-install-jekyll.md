---
layout: post
title: "install jekyll"
description: ""
category: 
tags: [jekyll]
---

Jekyll QuickStart

Host on GitHub
=====

1. Create a New Repository
===
Go to your https://github.com and create a new repository named
`USERNAME.github.com`

2. Install Jekyll-Bootstrap
===
```
$ git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
$ cd USERNAME.github.com
$ git remote set-url origin git@github.com:USERNAME/USERNAME.github.com.git
$ git push origin master
```

Run Jekyll Locally
=====

1. install ruby and jekyll
===
```
$ sudo yum install -y ruby ruby-devel
$ gem install jekyll rake rdiscount
```

2. Run Jekyll Locally
===
```
$ cd USERNAME.github.com
$ jekyll serve
```

3. Create a Post and page
===
```
$ rake post title="Hello World"
$ rake page name="about.md"
$ rake page name="pages/about.md"
$ rake page name="pages/about"
# this will create the file: ./pages/about/index.html
```

Publish
=====
```
$ git add .
$ git commit -m "Add new content"
$ git push origin master
```
