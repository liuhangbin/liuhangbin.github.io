---
layout: post
title: "python import config file"
categories:
tags: [python]
---

### import an ini file

ini file:

```
db_config.ini
[baseconf]
host=127.0.0.1
port=3306
user=root
password=root
db_name=evaluting_sys
[concurrent]
processor=20
```

python code:

```
#!/usr/bin/python
# -*- coding:utf-8 -*-
#author: lingyue.wkl
#desc: use to db ops
#---------------------
#2012-02-18 created

#---------------------
import sys,os
import ConfigParser

class Db_Connector:
  def __init__(self, config_file_path):
    cf = ConfigParser.ConfigParser()
    cf.read(config_file_path)

    s = cf.sections()
    print 'section:', s

    o = cf.options("baseconf")
    print 'options:', o

    v = cf.items("baseconf")
    print 'db:', v

    db_host = cf.get("baseconf", "host")
    db_port = cf.getint("baseconf", "port")
    db_user = cf.get("baseconf", "user")
    db_pwd = cf.get("baseconf", "password")

    print db_host, db_port, db_user, db_pwd

    cf.set("baseconf", "db_pass", "123456")
    cf.write(open("config_file_path", "w"))
if __name__ == "__main__":
  f = Db_Connector("../conf/db_config.ini")
```

result:

```
section: ['concurrent', 'baseconf']
options: ['host', 'db_name', 'user', 'password', 'port']
db: [('host', '127.0.0.1'), ('db_name', 'evaluting_sys'), ('user', 'root'), ('password', 'root'), ('port', '3306')]
127.0.0.1 3306 root root
```

### import a yaml file

data.yaml:

```
$ cat data.yaml
name: xiaorui
age: 18
spouse:
    name: mm
    age: 25
sb:
 - name: shencan
   age: 11
 - name1: dehua
   age1: 12
 - name2: qiaofeng
   age1: 13
```

python code:

```
#!/bin/python
import yaml
f = open('data.yaml')
x = yaml.load(f)
print x
print ""
print yaml.dump(x, width=1000)
```

result:

```
{'sb': [{'age': 11, 'name': 'shencan'}, {'age1': 12, 'name1': 'dehua'}, {'name2': 'qiaofeng', 'age1': 13}], 'age': 18, 'spouse': {'age': 25, 'name': 'mm'}, 'name': 'xiaorui'}

age: 18
name: xiaorui
sb:
- {age: 11, name: shencan}
- {age1: 12, name1: dehua}
- {age1: 13, name2: qiaofeng}
spouse: {age: 25, name: mm}
```

Reference:
You can install `PyYAML` for python yaml lib.
Also view `/usr/share/doc/PyYAML-3.10/examples/pygments-lexer/example.yaml` for yaml examples


### import a json file

data:

```
[
{"id" : "5"},
{"port" : 22},
{"node" : [
{"ip" : "1.1.1.1", "pwd" : "123456"},
{"ip" : "1.1.1.2", "pwd" : "123456"},
{"ip" : "2.2.2.1", "pwd" : "123456"}
]}]
```

output:

```
In [20]: import json
In [21]: v = json.load(file('config.json'))
In [22]: v
Out[22]:
[{u'id': u'5'},
{u'port': 22},
{u'node': [{u'ip': u'1.1.1.1', u'pwd': u'123456'},
{u'ip': u'1.1.1.2', u'pwd': u'123456'},
{u'ip': u'2.2.2.1', u'pwd': u'123456'}]}]
```

### import a xml file

data:

```
<?xml version="1.0" encoding="utf-8"?>
<config>
    <id>5</id>
    <port>22</port>
    <node>
        <ip>1.1.1.1</ip>
        <pwd>123456</pwd>
    </node>
    <node>
        <ip>1.1.1.2</ip>
        <pwd>123456</pwd>
    </node>
    <node>
        <ip>2.2.2.1</ip>
        <pwd>123456</pwd>
    </node>
</config>
```

python code:

```
$ cat config.py 
#! /usr/bin/env python
# -*- coding: utf-8 -*-
#

import xml.etree.ElementTree as ET

id = None
port = None
ipset = []

def load_config(file):
    root = ET.parse(file).getroot()

    global id, port
    id = root.find('id').text
    port = root.find('port').text

    nodes = root.findall('node')
    for node in nodes:
        d = {}
        d['ip'] = node.find('ip').text
        d['pwd'] = node.find('pwd').text
        ipset.append(d)

if __name__ == '__main__':
    print id
    print port
    print ipset
    load_config('config.xml')
    print id
    print port
    print ipset
```

output:

```
$ ./config.py 
None
None
[]
5
22
[{'ip': '1.1.1.1', 'pwd': '123456'}, {'ip': '1.1.1.2', 'pwd': '123456'}, {'ip': '2.2.2.1', 'pwd': '123456'}]
```
