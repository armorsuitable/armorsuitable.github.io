# 使用 Strace 抓取网络流量

主要监控一个进程在运行过程中，发起了哪些系统调用；如果收到其他进程发送的signal，也可以监控。
功能非常强大，因为不论是网络发送，监听，请求，都是调用操作系统的函数，比如listen、connect等；因此，可以监控网络调用，可以将方法调用时的入参、出参、花费时间等，都打印出来，或者打印到文件。

同时，可以监控文件，比如：
-t： 打印调用时间
-p ： 指定pid
-q : 压制attach、detach的消息，不然的话，attach到进程时，会打印一句提示；结束的时候，也会打印一堆提示。
-f： 不只是跟踪当前进程id对应的线程id，还要跟踪该pid内的全部线程，对于java这类多线程程序，尤其有用
-s strsize

Specify the maximum string size to print (the default is 32). Note that filenames are not considered strings and are always printed in full.
该参数的作用不便描述，可以看后面的例子。

-e trace=file

监控文件相关的系统调用

## 监控线程相关操作#

```bash
-e trace=process
```
Trace all system calls which involve process management. This is useful for watching the fork, wait, and exec steps of a process.

## 监控网络请求
```bash
strace -p [pid] -q -f -e trace=network
```

eg:

```bash
root@VM-96-20-centos ~]# strace -p 3314 -q -f  -e trace=network 2>&1 | grep POST
[pid 17795] sendto(2813, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid 23803] sendto(70, "POST / HTTP/1.1\r\nContent-Type: a"..., 1161, 0, NULL, 0) = 1161
[pid 23809] sendto(2839, "POST / HTTP/1.1\r\nContent-Type: a"..., 584, 0, NULL, 0 <unfinished ...>
[pid  4300] sendto(28, "POST / HTTP/1.1\r\nContent-Type: a"..., 584, 0, NULL, 0) = 584
[pid 23809] sendto(913, "POST / HTTP/1.1\r\nContent-Type: a"..., 584, 0, NULL, 0) = 584
[pid 23798] sendto(3013, "POST /interface.php HTTP/1.1\r\nAc"..., 2347, 0, NULL, 0) = 2347
[pid 23826] sendto(2298, "POST / HTTP/1.1\r\nContent-Type: a"..., 1158, 0, NULL, 0) = 1158
[pid 23399] sendto(2286, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid 17793] sendto(3013, "POST /interface.php HTTP/1.1\r\nAc"..., 2355, 0, NULL, 0) = 2355
[pid 23831] sendto(3013, "POST /interface.php HTTP/1.1\r\nAc"..., 2328, 0, NULL, 0) = 2328
[pid 23831] sendto(3013, "POST /interface.php HTTP/1.1\r\nAc"..., 2321, 0, NULL, 0) = 2321
[pid 23800] sendto(70, "POST / HTTP/1.1\r\nContent-Type: a"..., 1161, 0, NULL, 0) = 1161
[pid 23831] sendto(2935, "POST / HTTP/1.1\r\nContent-Type: a"..., 524, 0, NULL, 0) = 524
[pid  5820] sendto(3013, "POST /interface.php HTTP/1.1\r\nAc"..., 2352, 0, NULL, 0) = 2352
[pid 23461] sendto(396, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid  3926] sendto(3013, "POST /interface.php HTTP/1.1\r\nAc"..., 2355, 0, NULL, 0) = 2355
[pid 15034] sendto(2925, "POST / HTTP/1.1\r\nContent-Type: a"..., 584, 0, NULL, 0) = 584
[pid 15174] sendto(396, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid 23838] sendto(2904, "POST /interfaces/interface.php H"..., 672, 0, NULL, 0) = 672
[pid 22737] sendto(1187, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid  2021] sendto(913, "POST / HTTP/1.1\r\nContent-Type: a"..., 584, 0, NULL, 0) = 584
[pid  2021] sendto(28, "POST / HTTP/1.1\r\nContent-Type: a"..., 584, 0, NULL, 0) = 584
[pid 27232] sendto(2275, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid 17794] sendto(396, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid 23409] sendto(913, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid 23836] sendto(2256, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid 15026] sendto(3013, "POST /interface.php HTTP/1.1\r\nAc"..., 2328, 0, NULL, 0) = 2328
[pid 15026] sendto(3013, "POST /interface.php HTTP/1.1\r\nAc"..., 2321, 0, NULL, 0) = 2321
[pid 23766] sendto(913, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid  3556] sendto(2914, "POST /interface.php HTTP/1.1\r\nAc"..., 2351, 0, NULL, 0) = 2351
[pid 17795] sendto(1187, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid  3557] sendto(2895, "POST /interface.php HTTP/1.1\r\nAc"..., 2365, 0, NULL, 0) = 2365
[pid 17792] sendto(2839, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0 <unfinished ...>
[pid 15026] sendto(2925, "POST / HTTP/1.1\r\nContent-Type: a"..., 524, 0, NULL, 0 <unfinished ...>
[pid 23563] sendto(1938, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid 23832] sendto(2895, "POST /interface.php HTTP/1.1\r\nAc"..., 2328, 0, NULL, 0) = 2328
[pid  5826] sendto(2298, "POST / HTTP/1.1\r\nContent-Type: a"..., 647, 0, NULL, 0) = 647
[pid  4300] sendto(2603, "POST / HTTP/1.1\r\nContent-Type: a"..., 602, 0, NULL, 0) = 602
[pid 23832] sendto(2895, "POST /interface.php HTTP/1.1\r\nAc"..., 2321, 0, NULL, 0) = 2321
[pid 23810] sendto(28, "POST / HTTP/1.1\r\nContent-Type: a"..., 585, 0, NULL, 0) = 585
[pid  5826] sendto(2603, "POST / HTTP/1.1\r\nContent-Type: a"..., 647, 0, NULL, 0) = 647
[pid 23832] sendto(2256, "POST / HTTP/1.1\r\nContent-Type: a"..., 521, 0, NULL, 0) = 521
```

eg: 获取详细信息

```
[root@VM-96-20-centos ~]# strace -p 3314 -q -f -s 10000  -e trace=network 2>&1 | grep POST

[pid 23831] sendto(37, "POST / HTTP/1.1\r\nContent-Type: application/json;charset=UTF-8\r\nAccept: */*\r\nContent-Length: 963\r\nHost: 10.0.96.2:80\r\nConnection: Keep-Alive\r\nUser-Agent: Apache-HttpClient/4.5.13 (Java/1.8.0_232)\r\n\r\n{\"version\":\"1\",\"componentName\":\"MC\",\"eventId\":\"41fe52b4-c14a-4498-87a5-2faf72e0121d\",\"requestId\":\"41fe52b4-c14a-4498-87a5-2faf72e0121d\",\"requestVersion\":\"V3\",\"interface\":{\"interfaceName\":\"logic.xxx.sigAndAuth\",\"para\":{\"version\":\"2.0\",\"mode\":\"2\",\"method\":\"post\",\"ownerUin\":\"100001001014\",\"uin\":\"4611686018428091334\",\"action\":\"ckafka:DescribeACL\",\"resource\":\"qcs::xxx:gz:uin/xxxxxxxxx:xxx/xxx-xxx\",\"q-token\":\"xxxxxxxxxxxx-xxxxxxx-xxxx-xxxxxxx-xxxxxxxxx--xxxxxx-xxxxx-xxxxxxx\",\"condition\":{\"qcs:ip\":\"30.162.223.53\"}}}}", 1161, 0, NULL, 0) = 1161
[pid 23399] sendto(2923, "POST / HTTP/1.1\r\nContent-Type: application/json;charset=UTF-8\r\nAccept: */*\r\nContent-Length: 963\r\nHost: 10.0.96.2:80\r\nConnection: Keep-Alive\r\nUser-Agent: Apache-HttpClient/4.5.13 (Java/1.8.0_232)\r\n\r\n{\"version\":\"1\",\"componentName\":\"MC\",\"eventId\":\"2ba7970a-7cf5-4737-a88e-d306c10136c9\",\"requestId\":\"2ba7970a-7cf5-4737-a88e-d306c10136c9\",\"requestVersion\":\"V3\",\"interface\":{\"interfaceName\":\"logic.xxx.sigAndAuth\",\"para\":{\"version\":\"2.0\",\"mode\":\"2\",\"method\":\"post\",\"ownerUin\":\"100021726812\",\"uin\":\"4611686018430551662\",\"action\":\"xxx:DescribeACL\",\"resource\":\"qcs::xxx:gz:uin/100021726812:xxx/xxxx-zarvd5b3\",\"q-token\":\"\",\"condition\":{\"qcs:ip\":\"30.167.71.227\"}}}}", 1161, 0, NULL, 0) = 1161
[pid 23814] sendto(107, "POST /interfaces/interface.php HTTP/1.1\r\nContent-Type: application/json;charset=UTF-8\r\nAccept: */*\r\nContent-Length: 452\r\nHost: 10.0.96.10\r\nConnection: Keep-Alive\r\nUser-Agent: Apache-HttpClient/4.5.13 (Java/1.8.0_232)\r\n\r\n{\"version\":\"1\",\"componentName\":\"MC\",\"eventId\":\"xxxxxxxx-e31769c5ce38\",\"requestId\":\"xxxxxxxxxxxxx05-9ada-e31769c5ce38\",\"requestVersion\":\"v\",\"interface\":{\"interfaceName\":\"SubscribeGroup\",\"para\":{\"ownerUin\":\"000000\",\"appId\":0000000,\"uin\":\"00000000\",\"clientIp\":\"1.1.1.1\",\"Language\":\"en-US\",\"regionId\":1,\"offset\":0,\"limit\":10,\"instanceId\":\"xxxx-xxxxxx\",\"topicName\":\"xxxxxx\"}}}", 672, 0, NULL, 0) = 672
```


