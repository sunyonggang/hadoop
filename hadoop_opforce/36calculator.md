##计数器##
1.  计数器主要用来收集相关系统信息，以及作业运行时的统计信息，用于知道作业是成功了还是失败了；
2.  相比较而言，计数器比日志文件更容易分析。

###hadoop内置计数器###
1.  hadoop内置计数器主要用来记录作业的执行情况；
2.  内置计数器主要包括MapReduce框架计数器：
    *   文件系统计数器(FileSystemCounters)；
    *   作业计数器(JobCounters)；
    *   文件输入格式计数器(File Input Format Counters)；
    *   文件输出格式计数器(FileOutput Format Counters).
3.  计数器有相关task进行维护，定期传递给tasktracker，然后由tt传递给jobtracker；
4.  最终的作业计数器其实还是要jt进行维护，所以计数器可以全局汇总，也不必在网络上传输；
5.  只有当一个任务执行成功后，它的计数器值才是可靠的。

###自定义java计数器###
1.  MapReduce允许用户自定义计数器；
2.  计数器是一个全局变量；
3.  计数器有组的概念，可以使用枚举或字符串来创建；
4.  字符串类型比枚举类型更加灵活，可以在一个组下面创建多个计数器；
5.  在old API中使用Reporter,而在New API中使用Context.getCounter(groupname, countername)；
6.  计数器递增（increment（long））.

##计数器的使用###
1.  WEB ui方式；
2.  hadoop job -counter
3.  hadoop api中的job.getCounters(),而后调用counters.findCounter()得到计数器对象，查看最终计数器的值需要等作业完成.

###实践###

