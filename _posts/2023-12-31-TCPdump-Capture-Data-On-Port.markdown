# TCP Dump 抓包指定端口

## On Port

```shell
tcpdump -i any port 3306 -n -w 3306.pcap -v
```

## On Host

```bash
tcpdump -i any -s0 -n host 9.142.178.132  -w 0216.cap
```


参考
(tcpdump-part-one)[https://www.redhat.com/sysadmin/tcpdump-part-one]
