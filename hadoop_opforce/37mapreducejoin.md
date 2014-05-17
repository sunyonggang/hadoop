##MapReduce Join##
对数据库进行连接（编写MapReduce进行表的联接）
###联接###
使用SQL语言查询（37MapreduceJoin.jpg）
###Map端Join###
Map端的联接是指数据到达Map处理之前进行合并
基本私立
1.  需要join的两个文件，一个存储在hdfs中，一个使用DistributedCache.addCacheFile()将需要join的另一个文件加入到所有的Map缓存中；
2.  在Map中读取文件，进行join；
3.  将结果输出到reduce；
4.  DistributedCache.addCacheFile()需要在提交作业之前设置。

DistributedCache：DistributedCache是为了方便用户进行应用程序开发而设计的文件分发工具。它能够将只读的外部文件进行自动分发到各个节点上进行本地缓存，以便task运行时加载使用。
###使用步骤###
1.  在hdfs中上传文件；
2.  调用相关的API添加文件信息；
3.  task运行前直接调用文件读写api获取文件；
4.  常用API，DistributedCache.addCacheFile(), DistributedCache.addCacheArchive()。

##Reduce端的join##
reduce端的联接比map端的联接更普遍，因为输入的数据不需要特定的结构，但是效率低，因为所有文件必须经过shuffle过程。
###基本思路###
1.  map端读取所有的文件，并在输出的内容里加上标识代表数据从哪个文件里来的；
2.  在reduce处理函数里，对按照标识对数据进行保存；
3.  然后根据key的join来求出结果直接输出。