# JVM的参数

*标准阐述b*

```shell
-help
-server -client
-version -showversion
-cp -classpath
```

*X参数（非标准化参数）*

```txt
-Xint：执行解释
-Xcomp:第一次使用就编译成本地代码
-Xmixed: 混合模式，JVM自己来决定是否编译成本地代码
```

*XX参数*

--非标准化参数--

--相对不稳定--

--主要用于JVM调优和Debug--

```txt
 *Boolean类型*
 格式：-XX：[+-]<name> 表示启用或者禁用name属性
 比如：-XX:+UseConcMarkSweepGC
 	  -XX:+UseG1GC
 *非Boolean类型*
 格式：-XX:<name>=<value> 表示name的属性的值是value
 比如：-XX:MaxGCPauseMillis=500
 	  -XX:GCTimeRatio=1988
```

****

*-Xmx -Xms*

```txt
不是X参数，而是XX参数

-Xms等价于-XX:InitialHeapSize

-Xmx等价于-XX:MaxHeapSize
```

# 参看服务器进程的参数

链接: [jinfo命令使用](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jinfo.html#BCGEBFDD)

```shell
例子：
jinfo -flag MaxHeapSize <进程的PID>
jinfo -flags <进程的PID>
```

```shell
-XX:+PrintFlagsInital   查看初始zhi
-XX:+PrintFlagsFinal    查看最终值
-XX:+UnlockExperimentalVMOptions  解锁实验参数
-XX:+UnlockDiagnosticVMOptions   解锁诊断参数
-XX:+PrintCommandLineFlags  打印命令行参数
```

=表示默认值

:=修改过后的值

***

# Java8 各项Linux命令查询网站

[https://docs.oracle.com/javase/8/docs/technotes/tools/unix/index.html](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/index.html)

# jstat查看JVM统计信息

## -类装载

```shell
jstat -help   可以看看该命令的使用方式
options:-class,-compiler,-gc,printcompilation
例如：jstat -class <进程的PID> 1000 3  可以参见进程的类加载信息
                             每隔一秒钟输出3次
```

## -垃圾收集

```txt
-gc 、-gcutil 、 -gccause 、 -gcnew 、 -gclod
```

```txt
-gc输出结果：
S0C、S1C、S0U、S1U ： S0和S1的总量与使用量
EC、EU： Eden区总量与使用量
OC、OU ：Old区总量与使用量
MC、MU ： Metaspace区总量与使用量
CCSC、CCSU : 压缩类空间总量与使用量
YGC、YGCT : YoungGC的次数与时间
FGC、FGCT ： FullGC的次数与时间
GCT : 总的GC时间
```

## -JIT编译 

```shel
-compiler 、 -printcompilation
例如： jstat -compiler <进程PID>
        Java HotSpot VM即时编译器统计信息。
        Compiled：执行的编译任务数。
        Failed：编译任务失败次数。
        Invalid：已失效的编译任务数。
        Time：执行编译任务所花费的时间。
        FailedType：编译上次失败编译的类型。
        FailedMethod：上次失败编译的类名和方法。
```

# jmap+MAT 内存溢出

## 导出内存映像文件

### 内存溢出自动导出

```shell
-XX:+HeapDumpOnOutOfMemoryError 
-XX:+HeapDumpPath=./
```

### 使用jmap命令手动导出

```shell
jmap -help
option: -heap 、-clstats 、-dump:<dump-options>、-F 
例如：jmap -dump:format=b,file=heap.hprof <进程PID>
```

分析内存映像文件

http://www.eclipse.org/mat/downloads.php

在上述网站中下载对应的操作系统的分析工具

# jstack死循环与死锁

```shell
jstack <进程PID>  
#里面的线程号是 十六进制  top -p <进程PID> -H
#查到的PID需要使用  printf "%x" <线程PID>
#jstack <进程PID> >  lockLog.txt
```

# 进程远程的tomcat

```shell
#在本机上找到ava的安装目录后，找到Java VisuaVM这个文件
#启动后 在界面中找到远程
#监控远程的tomcat需要修改Catalina.sh
Java_OPTS="$JAVA_OPTS -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9004 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Djava.net.preferIPv4Stack=true -Djava.rmi.server.hostname=10.110.3.62"
#修改的是远程主机上的tomcat
```

## 监控普通的Java进程

```shell
nohup java -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9004 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Djava.net.preferIPv4Stack=true -Djava.rmi.server.hostname=10.110.3.62 -jar <jar包的名字.jar> &
```

##### 官方文档

```txt
jvisualVM：
https://docs.oracle.com/javase/8/docs/technotes/guides/visualvm/index.html
https://visualvm.github.io/documentation.html
jvisulaVM如何添加插件
https://visualvm.github.io/index.html
```

# btrace入门

git上的地址

```txt
btrace下载
https://github.com/btraceio/btrace
https://github.com/btraceio/btrace/releases/tag/v1.3.11
```

Btrace安装

```txt
windows上新建环境变量BTRACE_HOME
添加Path：%BTRACE_HOME%\bin
```

运行Btrace的方法

```shell
#在JVisualVM中添加Btrace插件，添加classpath
#使用命令行btrace <pid> <trace_script>
```

实例Btrace脚本

```shell
#在maven项目中加如Btrace的本地的jar包 pom.xml
#systemPath为本地所在的目录
```

```xml
<dependency> 
		    <groupId>com.sun.btrace</groupId> 
		    <artifactId>btrace-agent</artifactId> 
		    <version>1.3.11</version> 
		    <type>jar</type> 
		    <scope>system</scope> 
		    <systemPath>E:\course\JAVA应用性能监控与调优\软件\btrace-bin-1.3.11\build\btrace-agent.jar</systemPath> 
		</dependency>
		<dependency> 
		    <groupId>com.sun.btrace</groupId> 
		    <artifactId>btrace-boot</artifactId> 
		    <version>1.3.11</version> 
		    <type>jar</type> 
		    <scope>system</scope> 
		    <systemPath>E:\course\JAVA应用性能监控与调优\软件\btrace-bin-1.3.11\build\btrace-boot.jar</systemPath> 
		</dependency>
		<dependency> 
		    <groupId>com.sun.btrace</groupId> 
		    <artifactId>btrace-client</artifactId> 
		    <version>1.3.11</version> 
		    <type>jar</type> 
		    <scope>system</scope> 
		    <systemPath>E:\course\JAVA应用性能监控与调优\软件\btrace-bin-1.3.11\build\btrace-client.jar</systemPath> 
		</dependency>
```

```java
package com.imooc.monitor_tuning.chapter4;
import com.sun.btrace.AnyType;
import com.sun.btrace.BTraceUtils;
import com.sun.btrace.annotations.BTrace;
import com.sun.btrace.annotations.Kind;
import com.sun.btrace.annotations.Location;
import com.sun.btrace.annotations.OnMethod;
import com.sun.btrace.annotations.ProbeClassName;
import com.sun.btrace.annotations.ProbeMethodName;

@BTrace
public class PrintArgSimple {
	
	@OnMethod(
	        clazz="com.imooc.monitor_tuning.chapter4.Ch4Controller",
	        method="arg1",
	        location=@Location(Kind.ENTRY)
	)
	public static void anyRead(@ProbeClassName String pcn, @ProbeMethodName String pmn, AnyType[] args) {
		BTraceUtils.printArray(args);
		BTraceUtils.println(pcn+","+pmn);
		BTraceUtils.println();
    }
}
```

```txt
#拦截的时机
Kind.ENTRY : 入口。默认值
Kind.RETURN : 返回
Kind.THROW : 异常
Kind.Line : 行
```

# Tomcat远程Debug

```shell
#修改服务器中的Tomcat下 /bin/startup.sh 最后一行加上jpda
exec "$PRGDIR"/"$EXECUTABLE" jpda start "$@"
```

```shell
#修改服务器Tomcat中 catalina.sh 中的参数

```

![](images\tomcat.jpg)

```shell
#普通的Java进程，想使用远程DeBug address后为端口号
-agenlib:jdwp=transport=dt_socket,address=54321,server=y,suspend=n
```

## Tomcat-manager监控

*Tomcat-manager监控默认是关闭的*

**操作步骤**

1、conf/tomcat-users.xml添加用户

![](images\manager.png)

2、conf/Catalina/localhost/manager.xml配置允许的远程连接

​	*文件需要新建*

​	![](images/1.png)

3、重启

# psi-probe监控

git clone https://github.com/psi-probe/psi-probe

mvn clean package -Dmaven.test.skip

web/target/probe.war部署到Tomcat下

# Tomcat优化

## 线程优化

Tomcat官方说明文档位置：docs/config/http.html

![](images/2.png)

## 配置优化

##docs/config/context.html

reloadable:false

##conf/server.xml

protocol="org.apache.coyote.http11.Http11AprProtocol"

# Nginx性能监控与监控

​	nginx -V ： 查看nginx的信息

## ngx_http_stub_status监控连接信息

ngx_http_stub_status配置信息

```shell
#增加配置 编译时需要增加该模块 --with-http_stub_status_module
location = /nginx_status {
    stub_status on;
    access_log off;
    allow 127.0.0.1;   #允许访问的地址
    deny all;   #其他的全拒绝
}
```

## ngxtop监控请求信息

```txt
#安装python-pip
yum install epel-release
yum install python-pip
#安装ngxtop
pip install ngxtop
```

- ngxtop的使用方法

  git地址：https://github.com/lebinh/ngxtop

## nginx-rrd图形化监控

 http://www.linuxde.net/2012/04/9537.html

关闭防火墙

iptables -F

systemctl stop firewalld



1. 安装nginx

   修改yum源 

   ```shell
   cat /etc/yum.repos.d/nginx.repo
   [nginx]
   name=nginx.repo
   baseurl=http://nginx.org/packages/centos/7/$basearch/
   gpgcheck=0
   enabled=1
   ```

   安装：yum install nginx -y

   添加状态监控：

   ```shell
   location = /nginx_status {
       stub_status on;
       access_log off;
       allow 127.0.0.1;   #允许访问的地址
       deny all;   #其他的全拒绝
   }
   ```

2. 安装PHP

   ```shell
   yum insall php-gd php-soap php-mbstring php-xmlrpc php-dom php-fpm -y
   ```

3. nginx整合php-fpm

   修改/etc/php-fpm.d/www.conf文件中的user和group，与nginx.conf中的user一致

   user = nginx

   group = nginx

4. 启动php-fpm服务

   systemctl start php-fpm

5. 修改nginx的配置

   ```xml
   location ~\.php${
   	root /usr/share/nginx/html;
   	fastcgi_pass 127.0.0.1:9000;
   	fastcgi_index index.php;
   	fastcgi_param SCRIPT_FILENMF $document_root/$fastcgi_script_name;
   	include fastcgi_params;
   }  
   ```

6. 重启nginx

   ```shell
   nginx -s reload
   ```

7. 编写index.php

   ```php
   <?php phpinfo();?>
   ```

8. 打开浏览器访问；http://[服务器IP]/index.php

9. 安装rrdtool相关依赖

   ```shell
   yum install perl rrdtool perl-libwww-perl libwww-perl perl-rrdtool -y
   ```

10. 安装nginx-rdd

    ```shell
    wget http://soft.vpser.net/status/nginx-rrd/nginx-rrd-0.1.4.tgz
    tar zxvf nginx-rrd-0.1.4.tgz
    cd nginx-rrd-0.1.4
    cp usr/sbin/* /usr/sbin  #复制组程序文件到/usr/sbin下
    cp etc/nginx-rrd.conf /etc  #复制配置文件到/etc下
    cp html/index.php /usr/share/nginx/html/
    ```

11. 修改配置 

    ```shell
    vi /etc/nginx-rrd.conf
    RRD_DIR="/usr/share/nginx/html/nginx-rrd";
    WWW_DIR="/usr/share/nginx/html/";
    ```

12. 新建定时任务

    ```shell
    crontab -l
    * * * * * /bin/sh /usr/sbin/nginc-collect
    */15 * * * * /bin/sh /usr/sbin/nginx-graph
    ```

    查看任务：

    ```shell
    tail -f /var/log/cron
    ```

13. ab压测

    ```shell
    #安装ab压测工具 
    yum -y install httpd-tools
    ab -n 10000 -c 10 http://127.0.0.1/index.html
    ```

    # Nginx优化

    ```shell
    #增加工作线程数和并发连接数
    #启用长连接
    #勤工缓存、压缩
    #操作系统优化
    ```

    ## 配置线程数和并发数

    ```shell
    worker_processes 4; #cpu数
    events{
        worker_connections 10240;#每一个进程打开的最大连接数。包括了nginx与客户端和nginx与upstream之间的连接
        mulit_accept on;#可以一次建立多个连接
        use epoll;
    }
    ```

    ## 配置后端Server的长连接

    ```shell
    upstream server_pool{
        server localhost:8080 weight=1 max_fails=2 fail_timeout=30s;
        server localhost:8081 weight=1 max_fails=2 fail_timeout=30s;
        keepalive 300; #300个长连接
    }
    location /{
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_pass http://server_pool/;
    }
    ```

    ![](images/3.png)

## 配置压缩

```shell
gzip on;
gzip_http_version 1.1;
gzip_disable "MSIE[1-6]\.(?!.*SV1)";
gzip_proxied any;
gzip_types text/plian text/css application/javascript application/x-javascript application/json application/xml application/vnd.ms-fontobject application/x-font-ttf
application/svg+xml applicaton/x-icon;
gzip_vary on;#Vart:Accept-Encoding
gzip_static on;#如果有压缩好的直接用
```

## 操作系统优化

配置文件/etc/sysctl.conf

```shell
sysctl -w net.ipv4.tcp_syncookies=1;#防止一个套接字在有过多试图连接到达时引起过载

sysctl -w net.core.somaxconn=1024 #默认128，连接队列

sysctl -w net.ipv4.tcp_fin_timeout=10; #timewait的超时时间

sysctl -w net.ipv4.tcp_tw_reuse=1 #os直接使用timewait的连接

sysctl -w net.ipv4.tcp_tw.recycle=0 #回收禁止
```

## 其他优化

配置文件/etc/security/limits.conf

```shell
*hard nofile 204800
*soft nofile 204800
*soft core unlimited
*soft stack 204800 
```

#  垃圾收集器

## 串行收集器

web应用不使用

## 并行收集器

1. 吞吐量有限
2. -XX:+UseParallelGC , -XX:+UseParallelOldGC
3. Server模式下的默认收集器

## 并发收集器

1. 响应时间
2. CMS: XX:UseConcMarkSweepGC -XX:+UseParNewGC
3. G1： -XX:+UseG1GC

## 垃圾收集器搭配

![](images/4.png)

实线表示可以搭配

# 如何选择垃圾收集器

- 有限调整堆的大小让服务器自己来选择

- 如果内存小于100M，使用串行收集器

- 如果是单核，并且没有停顿时间的要求，串行或者JVM自己选择

- 如果允许停顿时间超过1秒，选择并行或者JVM自己选择

- 如果响应时间最重要，并且不能超过1秒，使用并行收集器

**官方文案**

https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/collectors.html

# Parallel Collector

- -XX:+UseparallelGC 手动开启，Server默认开启

- -XX:ParallelGCThreads=<N> 多少个GC线程

​	CPU>8 N=5/8

​	CPU<8 N=CPU

# G1 Collector

新生代和老年代收集器

Region

SATB:Snapshot-At-Beginning,它是通过Root Tracing得到的，GC开始时候存活的快照

RSet：记录了其他Region中的对象引用本Region中对象的关系，属于points-into结构（谁引用了我的对象）

# YoungGC

新对象进入Eden区

存活对象拷贝到Survivor区

存活时间达到年龄阈值，对象晋升到Old区

# MixedGC

不是FullGC,回收所有的Young和部分Old

global concurrent marking

```shell
-XX:+useG1GC  开启G1
-XX:+G1heapRegionSize=n , region的大小，1-32M , 2048个
-XX:MaxGCPauseMillis=200最大停顿时间
-XX:G1NewSizePercent \ -XX:G1MaxNewSizePercent
-XX:G1ReservePercent=10 保留防止 to space溢出
-XX:ParallelGCThreads=n SWT线程数
-XX:ConcGCThreads=n 并发线程数=1/4* 并行
```

# GC日志

打印日志相关参数

```shell
-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -Xloggc:$CATALINA_HOME/logs/gc.log -XX:+PrintHeapAtGC -XX:+PrintTenuringDistribution
```

```shell
#G1日志格式
https://blogs.oracle.com/poonam/understanding-g1-gc-logs
#GC日志分析工具
http://gceasy.io/   
#GCViewer
https://github.com/chewiebug/GCViewer
#ZGC：
http://openjdk.java.net/jeps/333
```

## GC调优设置

```shell
-XX:+DisableExplicitGC -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=$CATALINA_HOME/logs/ -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -Xloggc:$CATALINA_HOME/logs/gc.log
```

## ParallelGC调优

- 设置Matespace大小

  -XX:MatespaceSize=64M -XX:MaxMetaspaceSize=64M

- 增加吞吐量和停顿时间

  -XX:GCTimeRatio=99 -XX:MaxGCPauseMillis=100

- 修改动态扩容增量

  -XX:YoungGenerationSizeIncrement=30

## G1调优

- 年轻代大小：避免使用-Xmn、-XX:NewRatio等显式设置

  Young区大小，会覆盖暂停时间目标

- 暂停时间目标：暂停时间不要太严苛，其吞吐量目标是90%的应用程序时间和10%的垃圾回收时间，太严苛会直接影响到吞吐量

- 关于MixGC调优

  -XX:InitiatingHeapOccupancyPercent

  -XX:G1MixedGCLiveThresholdPercent

  -XX:G1HeapWastePercent

  -XX:G1MixedGCCountTarget

  -XX:G1OldCSetRegionThresholdPercent

G1**调优的相关参数**

```shell
-XX:+UseG1GC -Xms218M -Xmx128M 
-XX:MetaspaceSize=64M
-XX:MaxGCPauseMillis=100
-XX:+UseStringDeduplication
-XX:StringDeduplicationAgeThreshold=3
```

 