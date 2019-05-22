---
title: 常用Linux命令
copyright: true
abbrlink: c49a4fe0
date: 2017-01-16 17:29:15
categories:
  - NoteBook
tags:
  - Linux
top:
---

## 常用指令

ls　&ensp;&ensp;&ensp;&ensp;　        显示文件或目录

     -l           列出文件详细信息l(list)
     -a          列出当前目录下所有文件及目录，包括隐藏的a(all)
mkdir  &ensp;&ensp;&ensp;&ensp;　       创建目录

     -p           创建目录，若无父目录，则创建p(parent)
cd   &ensp;&ensp;&ensp;&ensp;　            切换目录

touch   &ensp;&ensp;&ensp;&ensp;　       创建空文件

echo   &ensp;&ensp;&ensp;&ensp;　         创建带有内容的文件。

cat    &ensp;&ensp;&ensp;&ensp;　          查看文件内容

cp     &ensp;&ensp;&ensp;&ensp;　           拷贝   cp [选项]... [-T] 源 目的

mv    &ensp;&ensp;&ensp;&ensp;　           移动或重命名

rm     &ensp;&ensp;&ensp;&ensp;　          删除文件

     -r            递归删除，可删除子目录及文件
     -f            强制删除
     
find    &ensp;&ensp;&ensp;&ensp;　          在文件系统中搜索某文件

wc      &ensp;&ensp;&ensp;&ensp;　          统计文本中行数、字数、字符数

grep    &ensp;&ensp;&ensp;&ensp;　         在文本文件中查找某个字符串

rmdir    &ensp;&ensp;&ensp;&ensp;　       删除空目录

tree     &ensp;&ensp;&ensp;&ensp;　        树形结构显示目录，需要安装tree包

pwd      &ensp;&ensp;&ensp;&ensp;　        显示当前目录

ln      &ensp;&ensp;&ensp;&ensp;　            创建链接文件

more、less &ensp;&ensp;&ensp;&ensp;　 分页显示文本文件内容

head、tail   &ensp;&ensp;&ensp;&ensp;　 显示文件头、尾内容

ctrl+alt+F1 &ensp;&ensp;&ensp;&ensp;　 命令行全屏模式
<!-- more -->
##　打包压缩相关命令

gzip：

bzip2：

tar:                打包压缩

     -c              归档文件
     -x              压缩文件
     -z              gzip压缩文件
     -j              bzip2压缩文件
     -v              显示压缩或解压缩过程 v(view)
     -f              使用档名
例：
tar -cvf /home/abc.tar /home/abc              只打包，不压缩

tar -zcvf /home/abc.tar.gz /home/abc        打包，并用gzip压缩

tar -jcvf /home/abc.tar.bz2 /home/abc      打包，并用bzip2压缩

当然，如果想解压缩，就直接替换上面的命令  tar -cvf  / tar -zcvf  / tar -jcvf 中的“c” 换成“x” 就可以了。


##　系统管理命令

stat    &ensp;&ensp;&ensp;&ensp;　          显示指定文件的详细信息，比ls更详细

who     &ensp;&ensp;&ensp;&ensp;　          显示在线登陆用户

whoami   &ensp;&ensp;&ensp;&ensp;　       显示当前操作用户

hostname  &ensp;&ensp;&ensp;&ensp;　    显示主机名

uname     &ensp;&ensp;&ensp;&ensp;　      显示系统信息

top     &ensp;&ensp;&ensp;&ensp;　           动态显示当前耗费资源最多进程信息

ps     &ensp;&ensp;&ensp;&ensp;　             显示瞬间进程状态 ps -aux

du    &ensp;&ensp;&ensp;&ensp;　              查看目录大小 du -h /home带有单位显示目录信息

df    &ensp;&ensp;&ensp;&ensp;　              查看磁盘大小 df -h 带有单位显示磁盘信息

ifconfig   &ensp;&ensp;&ensp;&ensp;　       查看网络情况

ping      &ensp;&ensp;&ensp;&ensp;　          测试网络连通

netstat    &ensp;&ensp;&ensp;&ensp;　      显示网络状态信息

man        &ensp;&ensp;&ensp;&ensp;　        命令不会用了，找男人  如：man ls

clear      &ensp;&ensp;&ensp;&ensp;　        清屏

alias     &ensp;&ensp;&ensp;&ensp;　          对命令重命名 如：alias showmeit="ps -aux" ，另外解除使用unaliax showmeit

kill      &ensp;&ensp;&ensp;&ensp;　           杀死进程，可以先用ps 或 top命令查看进程的id，然后再用kill命令杀死进程。