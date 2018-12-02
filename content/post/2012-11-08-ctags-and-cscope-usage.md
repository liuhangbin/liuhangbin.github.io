---
layout: post
Title: Ctags and cscope usage
Date: 2012-11-08
tags: [Linux, vim, ctags, cscope]
---

# Ctags

### Ctags Install

前提: 安装 vim

确认已安装了 ctags 
```
# which ctags
/usr/bin/ctags
```

如果结果如上，证明已经安装了该插件，则跳过下面步骤。

如果找不到，极有可能ctags还没安装，安装步骤如下：

1. 下载ctags：http://ctags.sourceforge.net
2. 解压,假设目录为:/tmp/ctags
3. 确保/tmp/ctags/configure文件有执行权限，执行该文件：/tmp/ctags/configure
4. 执行命令 `make && make install`

如果顺利，上面4个步骤可以成功安装ctags

### Ctags Config

在代码根目录运行命令 `ctags -R *`, 执行完毕后目录下应该有个 tags 文件

如果只有一个代码目录，可以在vimrc中设置tag文件位置`set tags=/workspace/weather/tags`


# Cscope

### Cscope Install

默认已经安装， 如果没有，下载网址: http://cscope.sourceforge.net/

1. 建立cscope使用的索引文件
    1. 在你需要浏览源码的根目录下（如你想用cscope看linux源码)使用下面命令： `# cscope -Rbkq`
    2. R 表示把所有子目录里的文件也建立索引
    3. b 表示cscope不启动自带的用户界面，而仅仅建立符号数据库
    4. q生成cscope.in.out和cscope.po.out文件，加快cscope的索引速度
    5. k在生成索引文件时，不搜索/usr/include目录
2. 在源码根目录下打开任意.c文件，使用如下命令：
    1. Ctrl+]将跳到光标所在变量或函数的定义处 Ctrl+T返回
    2. ：cs find s ---- 查找C语言符号，即查找函数名、宏、枚举值等出现的地方
       ：cs find g ---- 查找函数、宏、枚举等定义的位置，类似ctags所提供的功能
       ：cs find d ---- 查找本函数调用的函数
       ：cs find c ---- 查找调用本函数的函数
       ：cs find t: ---- 查找指定的字符串
       ：cs find e ---- 查找egrep模式，相当于egrep功能，但查找速度快多了
       ：cs find f ---- 查找并打开文件，类似vim的find功能
       ：cs find i ---- 查找包含本文件的文
    3. 2的所以命令也可以且按銉来实现：
       1. Ctrl+\ 再按 s 表示：cs find s命令
       2. 同理实现cs find + g,d,c,t,e,f,i命令
3. cscope_map.vim说明：
    1. 有英文注释的我就不说明了，我就说一下里边的键map映射
    2. 如： nmap <C-\>s :cs find s <C-R>=expand("<cword>")<CR><CR>
        1. nmap 表示在vim的普通模式下，即相对于：编辑模块和可视模式，以下是几种模式
            1.         :map            普通，可视模式及操作符等待模式
                       :vmap           可视模式
                       :omap           操作符等待模式
                       :map!           插入和命令行模式
                       :imap           插入模式
                       :cmap           命令行模式
            2. <C-\>表示：Ctrl+\
            3. s表示输入(即按：s)s
            4. : 表示输入':'
            5. “cs find s"表示输入"cs find s"也即是要输入的命令
            6. <C-R>=expand("cword")总体是为了得到：光标下的变量或函数。cword 表示：cursor word, 类似的还有：cfile表示光标所在处的文件名吧
            7. ＜CR＞＜CR＞就是回车吧，不太清楚

### Cscope Config

在vimrc中添加

```shell
nmap <C-_>s :cs find s <C-R>=expand("<cword>")<CR><CR>
nmap <C-_>g :cs find g <C-R>=expand("<cword>")<CR><CR>
nmap <C-_>c :cs find c <C-R>=expand("<cword>")<CR><CR>
nmap <C-_>t :cs find t <C-R>=expand("<cword>")<CR><CR>
nmap <C-_>e :cs find e <C-R>=expand("<cword>")<CR><CR>
nmap <C-_>f :cs find f <C-R>=expand("<cfile>")<CR><CR>
nmap <C-_>i :cs find i ^<C-R>=expand("<cfile>")<CR>$<CR>
nmap <C-_>d :cs find d <C-R>=expand("<cword>")<CR><CR>
```

当光标停在某个你要查找的词上时, 按下<C-_>g, 就是查找该对象的定义, 其他的同理. 

按这种组合键有一点技巧,按了<C-_>后要马上按下一个键,否则屏幕一闪就回到nomal状态了.

`<C-_>g`的按法是先按"Ctrl+Shift+-", 然后很快再按"g"。 

注意, 其中的这句: `nmap <C-_>i :cs find i ^<C-R>=expand("<cfile>")<CR>$<CR> ` 在有
的人的vim中无法工作, 可以改成`nmap <C-_>i :cs find i <C-R>=expand("<cfile>")<CR><CR>`试试.

### Normal Command

cscope的主要功能是通过同的子命令"find"来实现的 

"cscope find"的用法: 
```
　　cs find c|d|e|f|g|i|s|t name 

　　0 或 s 查找本 C 符号(可以跳过注释) 
　　1 或 g 查找本定义 
　　2 或 d 查找本函数调用的函数 
　　3 或 c 查找调用本函数的函数 
　　4 或 t 查找本字符串 
　　6 或 e 查找本 egrep 模式 
　　7 或 f 查找本文件 
　　8 或 i 查找包含本文件的文件
```

### Note

注意事项: 

　　1) 如果你是源码编译的vim，请使用 enable-cscope选项 

　　2) 目前只能在unix系列操作系统下使用cscope，虽然也有windows版本的cscope，不过还有很多bug。所以 

　　暂屈排在后面，但听说功能很强大，是很多linux用户使用VIM的必备插件之一。 

　　3) 安装设置内容比较多，这里先简单说一下， 详情可以参看以下网址: 

　　http://hi.baidu.com/kkernel/blog/item/6aac48b76129 90ff31add1a2.html 

　　或http://www.cnblogs.com/czh-liyu/archive/2009/11/21 /1607639.html 
