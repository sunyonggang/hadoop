##MapReduce重要组件分为三个部分##
1.  Combiner组件；
2.  Partitioner组件；
3.  RecordReader组件。

###Combiner组件###
1.  Combiner的作用是将一个Map产生的多个<key, value>合并为一个新的<key, value>，然后把新的<key, value>作为Reduce的输入；
2.  在Map函数与Reduce函数之间多了一个Combiner函数，目的是为了减少Map的输出的中间结果，这样减少了reduce复制Map的输出数据，减少了网络的传输量；
3.  并不是所有的情况下都可以使用Combiner的，对于一些求和问题可以使用，但对于求平均值，使用Combiner就不合适了。

###什么时候运行Combiner###
1.  job设置了Combiner，并且spill个数达到了min.num.spill.for.combine的时候，Combiner就会在merge之前执行；
2.  在有些情况下，merge开始执行，但spill的个数没有达到需求，这时Combiner就会在Merge之后进行；
3.  Combiner有可能不运行。

###Partitioner组件###
1.  Partitioner组件可以让Map对key进行分区，从而可以根据不同的key分发到不同的reduce中去；
2.  你可以自定义Partitioner使用自己的key的分发规则；
3.  提供默认的HashPartitioner继承Partitioner，并实现自己的getPartition。

###自定义的Partitioner###
1.  自定义抽象类Partitioner，实现getPartition();
2.  通过job.setPartitionClass()设置自定义的Partitioner。

###Partitioner的例子###
对于袜子，衣服，帽子等的周销售量的统计；

###RecordReader###
1.  以怎样的方式读取一条记录，每读取一条就会调用RecordReader类；
2.  系统默认的是LineRecordReader，如TextInputFormat，还有SequenceFileInputFormat的使用的是SequenceFileRecordReader；
3.  LineRecordReader的读取的key的内容为每行的偏移量，而内容作为value；
4.  应用场景：自定义读取key的类型。

###RecordReader的步骤###
1.  继承抽象类RecordReader，实现RecordReader的一个实例；
2.  实现自定义的InputFormat类，重写其中的CreateRecordReader方法，返回自定义的RecordReader对象；
3.  配置job.setInputFormatClass()，自定义InputFormat。

