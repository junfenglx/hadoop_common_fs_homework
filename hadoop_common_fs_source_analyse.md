目录
=======

[1 修订记录](#1-修订记录)

[2 摘要](#2-摘要)

[3 org.apache.hadoop.fs包总述](#3-orgapachehadoopfs包总述)

[4 Hadoop文件系统概述](#4-Hadoop文件系统概述)

1 修订记录
----------
序号 |        时间        | 修订人       | 版本
---- | ------------------ | ------------ | ----
1    | 2011 年 1 月 12 日 | 何芳、范永刚 | 1.0
2    | 2011 年 1 月 28 日 | 鲍亮         | 1.1
3    | 2014 年 5 月 18 日 | 213小组      | 1.2

*Hadoop版本: 2.4.0*

2 摘要
------
Hadoop是一个用于在通用硬件集群上进行大数据存储和处理的开源软件框架．

它主要包括以下几个模块：
* Hadoop Common. 包含其它模块需要的库和一些实用程序
* Hadoop Distributed File System (HDFS). 一个把数据存储在通用硬件的分布
式文件系统．在集群内能提供非常高的总计带宽
* Hadoop YARN. 用于管理集群内的计算资源和调度用户应用程序的资源管理平台
* Hadoop MapReduce. 一个用于大数据处理的编程模型．

所有的模块都在设计层面上考虑到了硬件错误．因此这些错误自动的在软件框架内被处理．

3 org.apache.hadoop.fs包总述
-----------------------------
org.apache.hadoop.fs包提供了一个抽象文件系统的 API．该包下有89个类和接口，有7个包．
如图3-1所示
![img][3-1.jpg]

其中有8个接口, 有四个抽象类: FileSystem, AbstractFileSystem, ChecksumFileSystem, TrashPolicy．

FileSystem 抽象类和 AbstractFileSystem 抽象类作为抽象文件系统
的基类,提供了基本的抽象操作。其中 FileSystem 类是 0.21 版本之前唯一的基类,但在 0.21 版本
中,出现了 AbstractFileSystem,该类似乎来取代 FileSystem 类原来的部分功能。在这两个基类的
基础上形成了两个类继承的层次结构。

org.apache.hadoop.fs 子包 ftp、 local、 s3、 s3native 和 viewfs 都是实现的具体的文件系统.
permission文件夹实现了有关文件访问许可的功能。shell 文件夹实现了对 shell 命令的调用。

4 Hadoop文件系统概述
-------------------
* [4.1 类层次结构](#41-类层次结构)
* [4.2 输入输出流](#42-输入输出流)
    * [4.2.1 Java中的IO](#421-Java中的IO)
    * [4.2.2 Hadoop中的IO](#422-Hadoop的IO)

###4.1 类层次结构

###4.2 输入输出流

####4.2.1 Java中的IO

####4.2.2 Hadoop中的IO



  [3-1.jpg]: ./images/3-1.jpg
