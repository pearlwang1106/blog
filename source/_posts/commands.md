---
title: 常用命令(持续更新)
date: 2018-10-09 10:55:26
tags: ['shell']
---

> 每日一言: 想在初雪的晚上去压马路

# shell 命令


- diff -urNa dir1 dir2
    - 说明：对比两个文件夹 diff
    - 参数解释：
```shell
-a  Treat  all  files  as text and compare them     
    line-by-line, even if they do not seem to be text.
-N, --new-file
    In  directory  comparison, if a file is found in
    only one directory, treat it as present but empty
    in the other directory.
-r  When comparing directories, recursively compare
    any subdirectories found.
-u  Use the unified output format.
```

- tree
    - 说明：生成树形结构目录
    - 安装：
```shell
brew install tree
```
    - 参数解释：
```shell
    -a 显示所有文件和目录。
    -A 使用ASNI绘图字符显示树状图而非以ASCII字符组合。
    -C 在文件和目录清单加上色彩，便于区分各种类型。
    -d 显示目录名称而非内容。
    -D 列出文件或目录的更改时间。
    -f 在每个文件或目录之前，显示完整的相对路径名称。
    -F 在执行文件，目录，Socket，符号连接，管道名称名称，各自加上"*","/","=","@","|"号。
    -g 列出文件或目录的所属群组名称，没有对应的名称时，则显示群组识别码。
    -i 不以阶梯状列出文件或目录名称。
    -I 不显示符合范本样式的文件或目录名称。
    -l 如遇到性质为符号连接的目录，直接列出该连接所指向的原始目录。
    -n 不在文件和目录清单加上色彩。
    -N 直接列出文件和目录名称，包括控制字符。
    -p 列出权限标示。
    -P 只显示符合范本样式的文件或目录名称。
    -q 用"?"号取代控制字符，列出文件和目录名称。
    -s 列出文件或目录大小。
    -t 用文件和目录的更改时间排序。
    -u 列出文件或目录的拥有者名称，没有对应的名称时，则显示用户识别码。
    -x 将范围局限在现行的文件系统中，若指定目录下的某些子目录，其存放于另一个文件系统上，则将该子目录予以排除在寻找范围外。
```