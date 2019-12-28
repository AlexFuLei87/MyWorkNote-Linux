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
```
## 2、对grep的理解

```shell
grep -C 10 keyword catalina.out 显示file文件中匹配keyword字串那行以及上下10行

grep -B 10 keyword catalina.out 显示keyword及前10行

grep -A 10 keyword catalina.out 显示keyword及后10行
```
