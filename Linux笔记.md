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
