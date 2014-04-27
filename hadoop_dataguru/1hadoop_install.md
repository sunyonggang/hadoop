hadoop1.2.1集群安装
===================
##准备工作##
*   三台机器，安装好linux，我使用的是CentOS6.5（64位），分为a,b,s，其中a，b作为datanode与tasktracker节点，s为namenode，jobtracker，secondarynamenode节点，下载好相应的jdk（1.7.0_55），hadoop-1.2.1，使用工具secureCRT，secureFX；
*   关闭linux的相关安全设置，防火墙，SElinux，ACL等；
*   安装jdk，在/etc/profile目录下进行相关设置，JAVA_HOME, PATH, CLASSPATH,然后source一下更新相关环境变量，测试jdk是否正确安装。

##开始##
*   完全分布式模式下配置免密码登录，关于RSA算法这里可以理解为只要拥有了公钥，那么就可以相互通信而不需要密码了，ssh-keygen命令给服务器端产生公私钥密钥对，scp命令将服务器端公钥复制到客户端（注意在伪分布模式下服务器端和客户端是同一台机器），因此客户端本身就拥有了
服务器端公钥，可以直接进行免密码接入。这里不仅namenode与datanode之间需要免密码，namenode与namenode本身也要进行免密码，把相关的公钥添加在.ssh文件下的authorized_keys里面。

##实施##
*   解压hadoop-1.2.1文件，进行配置：修改hadoop-env.sh中的JAVA_HOME，配置core-site.xml其中fs.default.name配置namenode的节点与端口，hadoop.tmp.dir配置hadoop产生数据的存储地址；配置hdfs-site.xml设置备份个数；配置mapred-site.xml设置jobtracker的节点与端口；
*   设置masters，slaves文件，将s，a，b的主机名填写，修改/etc/hosts文件，将s，a，b分别映射，可以使用ping测试；
*   使用scp向各个节点分发hadoop-1.2.1这个文件夹，目录注意要相同；
*   使用./hadoop namenode -format进行格式化，在bin目录下启动相关进程，然后使用jps在各个机器上查看是否正常启动；
*   编写测试文件a.txt，写入几个单词，将其上传到/user/root/input，然后进行计算**./hadoop jar ../hadoop-examples-1.2.1.jar wordcount input output**，使用*./hadoop fs -cat /user/root/output/part-r-00000*查看，得到结果：
c++     2
double  1
i       1
it      1
java    2
love    1
python  3
scala   1

##问题##
*   在core-site.xml中设置hadoop.tmp.dir，那么是不是无需在hdfs中设置name与data的存储目录？为什么不能存在/tmp目录下，我试过了我的tmp目录下的文件在重启后并不会丢失？
*   hadoop1.x在64位上与32位上都行吗？
*   *使用桥接的话，怎么设置使ip地址不产生变化？*