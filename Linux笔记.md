## 1、对expect的理解

```shell
# 拷贝远程文件到本地
/usr/bin/expect << EOF
spawn scp -r ${username}@${remote_ip}:${remote_dir} ${local_tmp_dir}
expect {
 "(yes/no)?"
  {
    send "yes\n"
    expect "*assword:" { send "${password}\n"}
  }
 "*assword:"
  {
    send "${password}\n"
  }
}
expect "100%"
expect eof
EOF

小坑：EOF后面确保没有空格
```
```shell
expect <<EOF  
    set timeout 10 
    spawn ssh $user@$ip 
    expect { 
         "yes/no" { send "yes\n";exp_continue } 
        "password" { send "$password\n" }    
        }     
    expect "]#" { send "useradd hehe\n" }  
EOF
```

## 2、对grep的理解

```shell
grep -C 10 keyword catalina.out 显示file文件中匹配keyword字串那行以及上下10行
grep -B 10 keyword catalina.out 显示keyword及前10行
grep -A 10 keyword catalina.out 显示keyword及后10行
#快速搜索在目录下面的含有关键字的文件
grep -r "待搜索关键词" 代码路径
-i  搜索时忽略大小写
-n  显示结果所在行号
-c  统计匹配到的总行数
-v  取反

```

## 3、tcpdum的使用

```shell
抓请求包：tcpdump -i any -Xs 0 -vv port 8080 -w 8080.cap

解析请求包：strings 8080.capp

//查看本机与mysql的操作命令 注意 -i any表示监听所有网络接口，我们也根据自身情况选择网络接口
# tcpdump -i any -w - dst port 3306 |strings  

//查看本机58895上与mysql的命令   注意 -i any 表示监听所有网络接口，我们需要根据自身情况选择网络接口
# tcpdump -i any -w - dst port 3306 and src port 58895 |strings 

```

## 4、xargs的使用

```shell
find /var/ -name "*.log" |xargs -I [] cp [] /tmp/ 【xargs 默认用是i表示{}，用I可以替换符号】
```

## 5、Linux中“!"的神奇用法

```shell
	!!            #再次执行上一条命令
	!find    #执行上条以find开头的命令
	================================
	$ history
    (这里省略更多内容)
    2043  touch ./dira/testfile
     2044  cp -rf dira dirb/
     2045  ls -al dira
     2046  ls -l dira
     2047  ls -al dira
     2048  ls -l dira
     2049  ls -al dira
     2050  ls -l dira
     2051  history 
    我们可以看到，history命令出来可以看到之前执行过的命令，也会看到它前面带了一个数值。如果我们想执行前面		的cp -rf dira dirb/命令，实际上只要用下面的方式即可：

    $ !2044   #2044是执行的第n条命令
```

## 6、Find的用法

```shell
　#find 对应目录 -mtime +天数 -name “文件名” -exec rm -rf {} ;
　#!/bin/sh
　find /usr/log -mtime +6 -name "server.log.*" -exec rm -rf {} \;
　exit
　
　find：linux的查找命令，用户查找指定条件的文件

　　/usr/local/backups：想要进行清理的任意目录

　　-mtime：标准语句写法

　　＋10：查找10天前的文件，这里用数字代表天数，＋30表示查找30天前的文件

　　"*.*"：希望查找的数据类型，"*.jpg"表示查找扩展名为jpg的所有文件，"*"表示查找所有文件，这个可以灵活运		用，举一反三

　　-exec：固定写法

　　rm -rf：强制删除文件，包括目录

　　{} \; ：固定写法，一对大括号+空格+\
```

jmap -histo 8264 > 8264.log
