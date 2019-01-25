
在 Linux 中，Cgroups 给用户暴露出来的操作接口是文件系统，即它以文件和目录的方式组织在操作系统的/sys/fs/cgroup 路径下

```
cd /sys/fs/cgroup
```
查看cpu的配置文件
```
cd cpu
ls /sys/fs/cgroup/cpu
```  
> cpu.cfs_period_us和cpu.cfs_quota_us  
> 限制进程在长度为cfs_period_us时间内，只能被分配到cfs_quota_us的时间

创建container
```
mkdir container
cd container
ll
```
操作系统会在新创建的 container 目录下，自动生成该子系统对应的资源限制文件

执行一个死循环，cpu将会占满
```
while : ; do : ; done &
top
```

查看container目录下的文件，看到container控制组里的CPU quota还没有任何限制（-1），CPU period则是默认的100ms
```
cat cpu.cfs_period_us
cat cpu.cfs_quota_us
```

向container组里的cfs_quota_us文件写入20ms
```
echo 20000 > cfs_quota_us
```

把被限制的进程的pid写入container组里的task文件
```
echo 226 > tasks
```

查看cpu使用情况
```
top
```

创建容器时可以指定
```
docker run -it --cpu-period=100000 --cpu-quota=20000 ubuntu /bin/bash
```
