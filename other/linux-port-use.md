# Linux查看端口被占用情况
---
## 使用lsof命令
---
lsof -i:端口号 用于查看某一端口的占用情况，比如查看2181端口使用情况，lsof -i:2181
```
jadsondeMacBook-Pro:~ jadson$ lsof -i:2181
COMMAND  PID   USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
java    3350 jadson   31u  IPv6 0xa8626f9f1aea1797      0t0  TCP *:eforward (LISTEN)
```

## netstat
---
netstat -tunlp |grep 端口号，用于查看指定的端口号的进程情况，如查看2181端口的情况，netstat -tunlp |grep 2181
```
# netstat -tunlp 
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address               Foreign Address             State       PID/Program name   
tcp        0      0 0.0.0.0:111                 0.0.0.0:*                   LISTEN      4814/rpcbind        
tcp        0      0 0.0.0.0:5908                0.0.0.0:*                   LISTEN      25492/qemu-kvm      
tcp        0      0 0.0.0.0:6996                0.0.0.0:*                   LISTEN      22065/lwfs          
tcp        0      0 192.168.122.1:53            0.0.0.0:*                   LISTEN      38296/dnsmasq       
tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN      5278/sshd           
tcp        0      0 127.0.0.1:631               0.0.0.0:*                   LISTEN      5013/cupsd          
tcp        0      0 127.0.0.1:25                0.0.0.0:*                   LISTEN      5962/master         
tcp        0      0 0.0.0.0:8666                0.0.0.0:*                   LISTEN      44868/lwfs          
tcp        0      0 0.0.0.0:8000                0.0.0.0:*                   LISTEN      22065/lwfs
```
```
# netstat -tunlp | grep 8000
tcp        0      0 0.0.0.0:8000                0.0.0.0:*                   LISTEN      22065/lwfs          
```
说明一下几个参数的含义：
```
-t (tcp) 仅显示tcp相关选项
-u (udp)仅显示udp相关选项
-n 拒绝显示别名，能显示数字的全部转化为数字
-l 仅列出在Listen(监听)的服务状态
-p 显示建立相关链接的程序名  
```