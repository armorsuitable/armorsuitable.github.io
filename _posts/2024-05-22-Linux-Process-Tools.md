# Linux Process Tools

## Get threads in specific process

```bash
ps -T -p [pid]
```

### Get thread detail
```bash
ps -xH | grep [pid]
```

## Get executable file path

```bash
ls /proc/{pid}/cwd -ld
```

eg:

```bash
[root@jgw-659868d479-xj87v /data/logs/nginx]# ls /proc/64/cwd -ld
lrwxrwxrwx 1 root root 0 10月  8 14:34 /proc/64/cwd -> /usr/local/services/tnginx_1_0_0-1.0/bin
```

## Get Process information

```bash
pidstat -p 239559
```
eg:
```bash
15:08:18          PID  minflt/s  majflt/s     VSZ    RSS   %MEM  Command
15:08:19        13084 133835.00      0.00 15720284 15716896  96.26  mmmm
15:08:20        13084  35807.00      0.00 15863504 15849756  97.07  mmmm
15:08:21        13084  19273.87      0.00 15949040 15792944  96.72  mmmm
```

### Get process starttime and duration time

```bash
ps -eo pid,lstart,etime,cmd | grep [keyword]
```

### Get process environment variable

```bash
ps eww -p ${PID}
```

### Get Top 10 processes resource occupied.

```bash
ps -aux|sort -k4nr|head -10
```

## Get Kernel core message log 

```bash
dmesg -Tx | tail
```
### Get Full message 

```bash
dmesg
```
