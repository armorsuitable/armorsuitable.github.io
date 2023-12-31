# ps 命令高级调试技巧

## 查看进程存在的线程

参数 T 查看进程中线程的概述
```bash
ps -T -p [pid]
```

## 查看进程中的线程完整信息

```bash
ps -xH | grep [pid]
```

## 查看进程执行的文件路径

```bash
ls /proc/{pid}/cwd -ld
```

```bash
[root@jgw-659868d479-xj87v /data/logs/nginx]# ls /proc/64/cwd -ld
lrwxrwxrwx 1 root root 0 10月  8 14:34 /proc/64/cwd -> /usr/local/services/tnginx_1_0_0-1.0/bin
```

## 查看进程中的线程CPU情况

使用 top -H -p pid 


```bash
top -H -p pid
```

## 进程状态资概览

```bash
pidstat -p 239559
```

输出结果

```bash
  
15:08:18          PID  minflt/s  majflt/s     VSZ    RSS   %MEM  Command
15:08:19        13084 133835.00      0.00 15720284 15716896  96.26  mmmm
15:08:20        13084  35807.00      0.00 15863504 15849756  97.07  mmmm
15:08:21        13084  19273.87      0.00 15949040 15792944  96.72  mmmm
```

主要用于监控全部或指定进程占用系统资源的情况，如CPU，内存、设备IO、任务切换、线程等。pidstat首次运行时显示自系统启动开始的各项统计信息，之后运行pidstat将显示自上次运行该命令以后的统计信息。用户可以通过指定统计的次数和时间来获得所需的统计信息。


ps 查询进程的启动时间，持续时间

```bash
ps -eo pid,lstart,etime,cmd 
```

https://jaminzhang.github.io/linux/using-ps-to-view-process-started-and-elapsed-time-in-linux/


ps 查看进程读取的环境变量

```bash
ps eww -p ${PID}
```

## 当前占用CPU/内存最多的10个进程

可以用以下命令查看使用内存最多的10个进程
```javascript
ps -aux|sort -k4nr|head -10
