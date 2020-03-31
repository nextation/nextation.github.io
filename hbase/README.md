## HBase极速体验
本文旨在帮助大家快速体验HBase环境，安装的是HBase的单机版环境，数据文件存储在本地文件系统而非HDFS，无需安装Hadoop。
### 1. 本文系统及软件环境
- 操作系统 CentOS Linux release 7.4
- JDK版本为 Oracle JDK 1.8.0
- HBase版本为 hbase-2.2.3
- JDK与HBase安装在 `/opt` 目录

### 2. HBase单机环境安装

#### 1）创建hbase用户
```
[root@ccnode1 opt]# useradd hbase
[root@ccnode1 opt]# passwd hbase
更改用户 hbase 的密码 。
新的 密码：
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。

```

修改 `/opt/hbase-2.2.3` 的所有者为 hbase 用户。

```
[root@ccnode1 opt]# chown -R hbase:hbase /opt/hbase-2.2.3
```

#### 2）添加JAVA_HOME环境变量
切换到hbase用户，编辑hbase用户的 `~/.bash_profile` 文件，增加JAVA_HOME设置
```
#增加以下内容
export JAVA_HOME=/opt/jdk1.8.0_131
export PATH=$JAVA_HOME/bin:$PATH

```

#### 3）添加HBASE_HOME环境变量
继续在hbase用户的 `~/.bash_profile` 文件中增加hbase环境配置
```
export HBASE_HOME=/opt/hbase-2.2.3
export PATH=$HBASE_HOME/bin:$PATH

```
修改完之后执行 `source ~/.bash_profile` 使配置生效。

#### 4）修改HBase配置文件

编辑HBASE_HOME目录下的conf/hbase-site.xml文件，添加上hbase.rootdir和hbase.zookeeper.property.dataDir的配置：
```xml
<configuration>
	<property>
		<name>hbase.rootdir</name>
		<value>/opt/hbase-2.2.3/data/hbase</value>
	</property>
	<property>
		<name>hbase.zookeeper.property.dataDir</name>
		<value>/opt/hbase-2.2.3/data/zookeeper</value>
	</property>
</configuration>

```

#### 5）启动HBase

执行 `start-hbase.sh` 启动HBase
```
[hbase@ccnode1 ~]$ start-hbase.sh 
running master, logging to /opt/hbase-2.2.3/logs/hbase-hbase-master-ccnode1.out

```
启动后，用jps命令看下启动的情况：

```
[hbase@ccnode1 ~]$ jps
1876 HMaster
2169 Jps

```
HMaster就是HBase的进程，单机版的HBase启动后只有一个进程。

打开浏览器，输入 http://XXX:16010 （XXX为你HBase的服务器ip地址），正常将看到如下界面
![image](https://netchick.github.io/hbase/hbase.png)

然后也可以 执行 `hbase shell` 命令进入shell客户端，只是因为还没有建表，所以list显示没有表。
```
[hbase@ccnode1 ~]$ hbase shell
2020-03-27 18:18:45,258 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
HBase Shell
Use "help" to get list of supported commands.
Use "exit" to quit this interactive shell.
For Reference, please visit: http://hbase.apache.org/2.0/book.html#shell
Version 2.2.3, r6a830d87542b766bd3dc4cfdee28655f62de3974, 2020年 01月 10日 星期五 18:27:51 CST
Took 0.0042 seconds                                                                                                                                                                
hbase(main):001:0> list
TABLE                                                                                                                                                                              
0 row(s)
Took 0.6857 seconds                                                                                                                                                                
=> []
hbase(main):002:0> 

```

### 附录：在Windows系统下安装HBase
我们知道，一般HBase都是安装在Linux下，极少安装在Windows系统下。但其实HBase是可以运行在Windows系统下的，不过目前也仅仅限于体验HBase。

- 操作系统 Windows 10

- JDK版本为 Oracle JDK 1.8.0

- HBase版本为 hbase-2.2.3

- HBase安装在 `/opt` 目录

- hadoop-2.6.5（winutils）

配置JAVA_HOME、HBASE_HOME环境变量、这里需要注意，还需要额外配置HADOOP_HOME环境变量，这里配置的是winutils hadoop-2.6.5。

还有一点要注意，需要将HBASE_HOME的lib/client-facing-thirdparty目录下的jar包拷贝至lib目录下，要不在Windows下启动hbase会报缺少类错误。

编辑HBASE_HOME目录下的conf/hbase-site.xml文件，添加上hbase.rootdir和hbase.zookeeper.property.dataDir的配置：

```
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>/opt/hbase-2.2.3/data/hbase</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/opt/hbase-2.2.3/data/zookeeper</value>
  </property>
  <property>
    <name>hbase.unsafe.stream.capability.enforce</name>
    <value>false</value>
  </property>
</configuration>
```

双击HBASE_HOME的bin目录下start-hbase.cmd文件即可启动HBase。