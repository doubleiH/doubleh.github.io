---
title: "linux 环境下导出db2数据出现的一些问题"
tags: [linux],[db2move]
excerpt: "由于行方提供的测试环境数据库默认设置编码格式为UTF-8,导致在导出数据时出现db2move.lst文件为空"
---
————————————————————————————————————————————————

###### 主要想记录下导出数据时出现编码格式不一致，导致数据导出失败（db2move导出db2数据库时报3107错误）解决办法。



解决办法：
```
db2move dbname export -u username -p password -aw
```

#### 重点是-aw选项，指示允许警告（allow warning），即在发生警告的情况下相关表的数据也会被导出。
该目录下会生成db2move.lst、EXPORT.out、*.ixf、*.msg文件
