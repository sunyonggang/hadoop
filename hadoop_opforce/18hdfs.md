18：hdfs详细分析三
==================
#常见的压缩算法：#
1.  DEFLATE
2.  gzip
3.  bzip2
4.  snappy

##源文件##
cd */hadoop-1.2.1/src/core/org/apache/hadoop/io/compress

##常见算法##
1. 其中DEFLATE是基于lz77与哈弗曼编码的一种无损压缩算法，源代码可以在zlib库中查找，gzip是基于DEFLATE的一种扩展；
2. 压缩算法其实是时间与空间的转化，在gzip中可以通过参数设置来制定，-1意味着速度，-9意味着空间；
3. gzip的时间与空间比较适中，bzip2的速度不如gzip但压缩效果最好，可分割；
4. codex的原生库。

##查看hadoop的org.apache.hadoop.io.compress Interface CompressionCodec##
里面拥有createInputStream(InputStream ), createOutputStream(OutputStream);
我们可以查看snappycodec，里面LoadSnappy.isLoaded();通过这个方法我们找到：System.loadLibrary("snappy");确认有没有libsnappy.so。

##snappy&zlib##
确认安装了zlib；
设置hadoop.native.lib为true；
在java.library.path设置库文件libhadoop，libsnappy。

####