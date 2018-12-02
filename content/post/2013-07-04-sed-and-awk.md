---
layout: post
Title: Sed And Awk Usage
Date: 2013-07-04
tags: [Linux, Sed, Awk]
---

=== Sed Usage
sed -e 's/[^ ]* //'  file  //删除文本文件第一列
sed '1,3d' file //删除1-3行
sed '/[Aa]bc/p' file //打印包含Abc或者abc的行
sed '/ABC/d' //删除包含ABC的行
sed '/ABC/!d' //不删除包含ABC的行
sed '5,$d' //从第五行到最后一行全部删除
sed 1q  //显示第一行
sed 's/$/.../g' //每行的最后面添加 ...
sed 's/d.(sk)/1&/g' //满足d*sk的单词,在单词前添加sk
sed 's/<[^>]*>//g' //去除html标签
sed '/ABC/a123' //在包含ABC的行后面添加一行数值为123的行
sed '/ABC/i123' //跟a不同的是i是插入当前行的前面
sed '/ABC/c123' //包含ABC的行将更改为123
sed '/ABC/{n;s/123/ABC/g}' //精确匹配包含ABC的下一行,将123全部替换成ABC
sed '/ABC/{s/A/a/}' //在包含ABC的行将A替换成a
sed -e '/ABC/h' -e '$g' //将包含ABC的行复制,在最后一行添加.
sed -e '/ABC/{h;d}' -e '/123/G'
//将包含ABC的行复制并删除,在包含123的行后添加.g等于在当前行覆盖,G是在符合条件的行后新增加一行.
sed 's/^/ /g' //每行前面增加一个空格
sed 's/.(.)$/*1/' //将倒数第二位换成*
sed '/ABC/!s/1/*/g' //在不包含ABC的行里,将所有的1替换成*
sed 's/1|2|3/*/g' //在GNU里将不管是1或2或者是3替换成*
sed '2 i\xxx' b.txt  //在b.txt的第2行之前插入“xxx”
sed '2 a\xxx' b.txt  //在b.txt的第2行之后插入“xxx”
sed '2,3 a\xxx' b.txt  //在b.txt的第2行和第3行之后插入“xxx”
sed '2,3 i\xxx' b.txt  //在b.txt的第2行和第3行之前插入“xxx”
sed '2,$ i\xxx' b.txt  //在b.txt的第2行至最后一行之前插入“xxx”
sed 's/^abc/123&/' a.txt  //在a.txt的abc行前插入123
sed 's/abc.*$/&123' a.txt //在a.txt的abc行后插入123

文件test内容如下：
aaa = test1, test2
bbb = acd, bacd
ccc = 123, 12345
 
需要在匹配‘bbb = ’ 的行尾追加字符串‘, abcdefg’
 
aaa = test1, test2
bbb = acd, bacd, abcdefg
ccc = 123, 12345
 
sed -i 's/bbb=.*/&,abcdefgh/' test.txt

1.删除文件每行的第一个字符
sed 's/^.//' /etc/passwd
2.删除文件每行的第二个字符
sed -r 's/'^( . ) ./\1/' /etc/passwd
3.删除文件每行的最后一个字符
sed 's/.$//' /etc/passwd
4.删除文件每行的倒数第二个字符
sed 's/. ( .)$/\1/' /etc/passwd
5.删除文件每行的第二个单词
sed -r 's/^( [a-Z]+[^a-Z]+)[a-Z]+/\1/' /etc/passwd
6.删除文件每行的倒数第二个单词
sed -r 's/[a-Z]+([^a-Z]+[a-Z]+)$/\1/' /etc/passwd
7.删除文件每行的最后一个单词
sed -r -e's/[a-Z]+$//' -e's/[a-Z]+(:)/\1/'/etc/passwd
8.交换每行的第一个字符和第二个字符
sed -r 's/'^( . ) ( . )/\2\1/' /etc/passwd
9.交换没行的第一个字符和第二个单词
sed -r 's/^( .)( [a-z]+[^a-Z]+)( [a-Z]+)/\3\2\1/'/etc/passwd 非数字帐户
10 交换没行的第一个单词和最后一个单词
sed -r 's/^( [a-Z]+)( [^a-z].*[^a-Z]) ([a-z]+)$/\3\2\1/' /etc/passwd 非数字帐户
11 删除一个文件中所有的数字
sed -r 's/[0-9]//g' /etc/passwd
12 删除每行开头的所有空格
sed -r 's/^[ ]//g' /etc/passwd
13 用制表符替换文件中出现的所有空格
sed -r 's/[ ]+/\t/g' /etc/passwd
14 把所有大写字母用（）括起来
sed -r 's/( [A-Z])/(\1)/g' /etc/passwd
15 打印每行三次
sed '{p;p; } ' /etc /passwd
16 隔行删除
sed -n '1~2p' /etc/passwd
17 把文件从第22 行到第33 行复制到第56 行后面
sed -e'22h' -e'23,33H' -e'56G' /etc/passwd
18 把文件从第22 行到第33 行移动到第56 行后面
sed -e'22h' -e'23,33H' -e'56G' -e '22,33d' /etc/passwd
19 只显示每行的第一个单词
sed -r 's/^( [a-Z]+)[^a-z] .*/\1/' /etc/passwd 非数字用户
20 打印每行的第一个单词和第三个单词
sed -r 's/^( [a-Z]+)[^a-Z]+[a-Z]+[^a-Z]+（[a-Z]+）[^a-Z] .*/\1 \2/'/etc/passwd
21 将格式为 mm/yy/dd 的日期格式改为 mm;yy;ddsed-r's@/@;@g'/etc/passwd

22 替换换行
echo -e "hi\nyou" | sed 'N;s/\n/../'




=== Awk Usage

awk '{$1="";print $0}'  file  \\删除文本文件第一列
 
1. 功能是打印所有输入行
awk '{print $0} ' f ilename
2. 打印输入文件第八行
awk 'NR==8{print $0} ' f ilename
3. 用awk打印文件所有行的第一个字段
awk -F:'{print $1} ' f ilename
4. 打印输入行总数
awk 'BEGIN{num=0;} {num++;}END{print num} ' f ilename
awk 'END{print NR}' f ilename
5. 打印每行字段数
awk -F: '{print NF,$0} ' f ilename
6. 打印最后一行的最后一个字段的值
Awk -F: 'END{print $NF} ' filename
7. 打印字段数大于等于4个的行
Awk -F: ‘NF>＝4｛print $0｝ ’ f ilename
8. 打印文件所有字段的总数
Awk -F: 'BEGIN{count=0}{count=count+NF:}{print count} ' f ilename
9. 打印UID在 30－40范围内的用户名
Awk -F: '$>=30&&S3<=40{print $1}' f ilename
10.倒叙排列文件的所有字段
awk -f : '{for( i=NF;i>0:i--) }{ if ( i!=1} {printf "%s: " ,$i};}else{printf ("%s\n" ,$i) ; }
#!/bin/awk -f
BEGIN{
F=": "
}
{
for( i=NF;i>0;i--)
{
if( i!=1)
｛
printf ("%s: " ,$i)
｝
Else
{
printf ("%s\n",$i)
}
}
}
11.打印5 到26 行
Awk -F: 'NR>=5&&NR<=26{print} ' f ilename12.在文件顶部加上标题“Document“
Awk 'BENGIN｛print“Document｝{print} 'f ilename
13.隔行删除
Awk '{ if (NR%2==1) {print } }' f ilename
14.打印字段数大于5的行的总数
Awk -F: 'BEGIN{num=0; } { if(NF>=5) {num++;}} END {print num;}' f ilename
15.编写一个 awk脚本，统计／dev中各类型文件数量
#!/bin/awk -f
BEGIN{
ff=0
dd=0
bb=0
cc=0
pp=0
ss=0
ll=0
}
{
if($0 ~/^-/)
｛
ff++
｝
if($0 ~/^p/)
｛
pp++
｝
if($0 ~/^b/)
｛
bb++
｝
if($0 ~/^/)
｛
cc++
｝
if($0 ~/^d/)
｛
dd++
｝
if($0 ~/^l/)
｛
ll++
｝
if($0 ~/^s/)
｛s++
｝
}
END{
Print "f ile: ", f f
Print "directory: " ,dd
print" character: ",cc
print" block: " ,bb
print" link: " ,ll
print" sockt : ",ss
print" pipe: " ,pp
}
16.编写一个 awk脚本，读取IP来自一个文件，并且测试该IP地址
#!/bin/awk -f
{
if(system("ping-c 1 " $0">/dev/null)==0)
{
Print $0 YES
}
else
{
Print $0 NO
}
}
17.编写一个 awk脚本，读取文件添加用户
#!/bin/awk -f
{
If (system("useradd" $0 2>/dev/null) ==0)
{
Print "user: " $0"add"
}
else
{
Print "user"$0"error"
}
}
18.编写一个 awk脚本，功能过滤出合法IP地址
#!/bin/awk -f
BEGIN{
FS=" : "
}
{
if( 1>0&&$1<255&&$2>=0&&$2<=255$3>=0&&$3<=255$4>=0&&$4<=255{
Print $0
}
}
19.编写一个 awk脚本，功能是每行抽取第一个单词
#!/bin/awk -f
BEGIN{
FS=" [^a-Z]+"
}
{ f irst=0
f lag=0
for( i=1;i<=NF;i++)
{
If ($I ！＝NULL｝
｛
if(f lag==0)
{
f irst=I
f lag=1
}
｝
}
Print $f irst
}
20.编写一个 awk脚本，功能是打印每行的第一个和最后一个单词
#!/bin/awk -f
BEGIN{
	FS=" [^a-Z]+"
}
{ first=0
flag=0
last=0
for( i=1;i<=NF;i++)
{
	If ($I ！＝NULL｝
			｛
			last=i
			if(f lag==0)
			{
			f irst=I
			f lag=1
			}｝
			}
			Print $f irst " " $last
}
