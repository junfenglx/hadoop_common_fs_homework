目录
=======

[1 修订记录](#1-修订记录)

[2 摘要](2-摘要)

[3 org.apache.hadoop.fs包总述](#3-orgapachehadoopfs包总述)

[4 Hadoop文件系统概述](#4-hadoop文件系统概述)

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
org.apache.hadoop.fs包提供了一个抽象文件系统的 API．该包下有80多个类和接口，有7个包．
如图3-1所示
![img][3-1.jpg]

其中有8个接口, 有四个抽象类: FileSystem, AbstractFileSystem, ChecksumFileSystem, TrashPolicy．

FileSystem 抽象类和 AbstractFileSystem 抽象类作为抽象文件系统
的基类,提供了基本的抽象操作。其中 FileSystem 类是 0.21 版本之前唯一的基类,但在 0.21 版本
中,出现了 AbstractFileSystem,该类似乎来取代 FileSystem 类原来的部分功能。在这两个基类的
基础上形成了两个类继承的层次结构。

org.apache.hadoop.fs 子包 ftp、 local、 s3、 s3native 和 Hdfs 类都是实现的对具体的文件系统操作的类.
permission文件夹实现了有关文件访问许可的功能。shell 文件夹实现了对 shell 命令的调用。

4 Hadoop文件系统概述
-------------------
* [4.1 类层次结构](#41-类层次结构)
* [4.2 输入输出流](#42-输入输出流)
    * [4.2.1 Java中的IO](#421-java中的io)
    * [4.2.2 Hadoop中的IO](#422-hadoop的io)

###4.1 类层次结构
Hadoop 文件系统可以访问多个不同的具体的文件系统,如 HDFS、LocalFS和 S3 文件系统。不
同的文件系统具有不同的具体实现,Hadoop fs包下实现的是一层类似 Linux 中的 VFS 虚拟文件系统,它从不同
的文件系统中抽取了共同的操作,这些操作是一般的文件系统都具有的操作,如打开文件,创建
文件,删除文件,复制文件,获取文件的信息等。这些共同的基本操作组合在一起就形成了
FileSystem 抽象类和 AbstractFileSystem 抽象类。然后从基类派生,以实现对不同文件系统的统一操作。

*注解:*
HDFS是Hadoop下的另一子模块，它是一个分布式文件系统，是具体的文件系统实现．它和S3, LocalFS都是文件系统.
而org.apache.hadoop.fs包是对不同文件系统的抽象表示，它建立了一个抽象的统一的文件系统接口.
应用程序开发者通过fs包可以和不同的文件系统交互，而不用知道它所进行的操作所在的具体是什么文件系统．
而不同的文件系统开发者则可以根据fs包很容易的使其它文件系统支持Hadoop.

fs包下的类继承层次结构如图 4-1 所示:
![img][4-1.png]
fs各子包下的类继承层次结构如图4-2所示:
![img][4-2.png]

除 FilterFileSystem 外,FileSystem 的直接子类都是跟具体文件系统交互的类。包括以下子类:
* 由FileSystem派生:
    * S3FileSystem(org.apache.hadoop.fs.s3) - 数据以块形式存储在[Amazon S3][S3]
    * NativeS3FileSystem(org.apache.hadoop.fs.s3) - 文件以原生格式存储在[Amazon S3][S3]
    * ChRootedFileSystem(org.apache.hadoop.fs.viewfs) - 根目录为已有文件系统的某个路径的文件系统
    * ViewFileSystem(org.apache.hadoop.fs.viewfs) - 实现了客户端的挂载表
    * DistributedFileSystem(org.apache.hadoop.hdfs) - 为DFS system 实现了抽象类FileSystem. 用户代码通过该类和Hadoop DistributedFileSystem交互
    * LocalFileSystem - 为还有校验功能的本地文件系统实现了FileSystem API(内部使用RawLocalFileSystem, Decorator Pattern)
    * RawLocalFileSystem - 为原生本地文件系统实现了FileSystem API
    * FTPFileSystem(org.apache.hadoop.fs.ftp) - 基于FTP协议和FTP服务器交互的FileSystem API实现
* 由AbstractFileSystem派生:
    * RawLocalFs(org.apache.hadoop.fs.local) - 内部委派给RawLocalFileSystem来实现AbstractFileSystem API(delegation pattern)
    * FtpFs(org.apache.hadoop.fs.ftp) - 内部委派给FTPFileSystem来实现AbstractFileSystem API(delegation pattern)
    * LocalFs(org.apache.hadoop.fs.local) - 和LocalFileSystem的实现类似, 继承ChecksumFs. (内部使用RawLocalFs实现, Decorator Pattern)
    * ViewFs(org.apache.hadoop.fs.viewfs) - 完全在客户端的内存中实现挂载表
    * Hdfs - 为Hadoop DistributedFileSystem实现了AbstractFileSystem API

*注解:*
* LocalFileSystem是加上Checksum功能的本地文件系统，该类和操作系统本地文件系统交互，内部使用RawLocalFileSystem.
RawLocalFileSystem是代表没有Checksum功能的操作系统本地文件系统．
* 继承自AbstractFileSystem的LocalFS和RawLocalFs结构同上．其内部实现被委派给RawLocalFileSystem.
* Amazon S3没有5G限制


Hadoop 的使用者可以分为两类,应用程序编写者和文件系统实现者。在 Hadoop 0.21 版本之
前, FileSystem 类作为一般(抽象)文件系统的基类,一方面为应用程序编写者提供了使用 Hadoop
文件系统的接口,另一方面,为文件系统实现者提供了实现一个文件系统的接口(如 hdfs,本地
文件系统,FtpFs等等)。

但在 Hadoop 0.21 版本中,出现了 FileContext 类和 AbstractFileSystem 类,
通过这两个 API,可以将原来集中于 FileSystem 一个类中的功能分开,让使用者更加方便的在应
用程序中使用多个文件系统。

FileContext 这个 API 还没有在 hadoop 中被大量的使用,因为还没有
被合并到 mapreduce 计算中,但是它包含了正常的 FileSystem 接口没有的新功能,如支持 hdfs
层面的软链接等。

FileContext 类是用来取代 FileSystem 类,向 应用程序编写者 提供使用 Hadoop
文件系统的接口,而原来的 FileSystem 则仅由 文件系统实现者 使用。估计 AbstractFileSystem 类将
来会取代 FileSystem 类。

从图 4-1, 4-2 中可以看出 AbstractFileSystem 对应 FileSystem,FilterFs 对应 FiterFileSystem,
ChecksumFs 对应 ChecksumFileSystem,LocalFs 对应 LocalFileSystem, RawLocalFs 对应 RawLocalFileSystem。

而在各个具体的文件系统类, FtpFs 和RawLocalFs
通过DelegateToFileSystem(delegation pattern)委派给已有的FTPFileSystem, RawLocalFileSystem,
ViewFs, hdfs的实现是根据各个文件系统的特点直接实现的

###4.2 输入输出流

####4.2.1 Java中的IO

####4.2.2 Hadoop中的IO



  [3-1.jpg]: ./images/3-1.jpg
  [4-1.png]: ./images/4-1.png
  [4-2.png]: ./images/4-2.png
  [S3]: http://aws.amazon.com/s3
