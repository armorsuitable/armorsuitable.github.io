TCP Dump 相关使用说明

## 获取 HTTP 数据包

### HTTP Request 数据包

```bash
tcpdump -s 0 -A 'tcp dst port 50020 and (tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x504f5354)'
```

其中 `50020`  为服务所在的端口号


### HTTP Response 数据包

```bash
tcpdump -X -s 0 'tcp port 50020 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'
```

其中 `50020` 为服务所在端口号


### 结合 nsenter 抓取容器内容的数据包

```bash
nsenter -t 29120 -n -- tcpdump -nn -vv port 8080
```

其中 `29120` 为服务所在的 pid,  `8080` 为服务所在的端口号


## 数据包分析

### 示例数据片段


```txt
16:02:27.627692 IP (tos 0x68, ttl 54, id 14232, offset 0, flags [DF], proto TCP (6), length 60)
    10.0.0.13.37036 > 192.168.3.44.8080: Flags [S], cksum 0x8fb8 (correct), seq 2141526997, win 29200, options [mss 1424,sackOK,TS val 7744589 ecr 0,nop,wscale 7], length 0
16:02:27.627725 IP (tos 0x0, ttl 64, id 0, offset 0, flags [DF], proto TCP (6), length 52)
    192.168.3.44.8080 > 10.0.0.13.37036: Flags [S.], cksum 0xce07 (incorrect -> 0x588a), seq 3714559751, ack 2141526998, win 28800, options [mss 1440,nop,nop,sackOK,nop,wscale 9], length 0
16:02:27.665258 IP (tos 0x68, ttl 54, id 14233, offset 0, flags [DF], proto TCP (6), length 40)
    10.0.0.13.37036 > 192.168.3.44.8080: Flags [.], cksum 0x08e6 (correct), seq 1, ack 1, win 229, length 0
16:02:27.665396 IP (tos 0x68, ttl 54, id 14234, offset 0, flags [DF], proto TCP (6), length 492)
    10.0.0.13.37036 > 192.168.3.44.8080: Flags [P.], cksum 0x1344 (correct), seq 1:453, ack 1, win 229, length 452: HTTP, length: 452
	PUT /4MB.in5577006791947779410 HTTP/1.1
	Host: 636c-cloud-runner154-18f284.web3.tcb-tcs-test.cloud.com
	User-Agent: cos-go-sdk-v5/0.7.12
	Transfer-Encoding: chunked
	Accept-Encoding: gzip
	
16:02:27.665408 IP (tos 0x0, ttl 64, id 696, offset 0, flags [DF], proto TCP (6), length 40)
    192.168.3.44.8080 > 10.0.0.13.37036: Flags [.], cksum 0xcdfb (incorrect -> 0x07cc), seq 1, ack 453, win 59, length 0
16:02:27.665502 IP (tos 0x68, ttl 54, id 14235, offset 0, flags [DF], proto TCP (6), length 4136)
    10.0.0.13.37036 > 192.168.3.44.8080: Flags [P.], cksum 0xddfb (incorrect -> 0x81af), seq 453:4549, ack 1, win 229, length 4096: HTTP
16:02:27.665519 IP (tos 0x0, ttl 64, id 697, offset 0, flags [DF], proto TCP (6), length 40)
    192.168.3.44.8080 > 10.0.0.13.37036: Flags [.], cksum 0xcdfb (incorrect -> 0xf7bb), seq 1, ack 4549, win 75, length 0
16:02:27.665504 IP (tos 0x68, ttl 54, id 14238, offset 0, flags [DF], proto TCP (6), length 8584)
    10.0.0.13.37036 > 192.168.3.44.8080: Flags [.], cksum 0xef5b (incorrect -> 0xd5c1), seq 4549:13093, ack 1, win 229, length 8544: HTTP
16:02:27.665526 IP (tos 0x0, ttl 64, id 698, offset 0, flags [DF], proto TCP (6), length 40)

... 

16:02:28.022378 IP (tos 0x0, ttl 64, id 32687, offset 0, flags [DF], proto TCP (6), length 8232)
    192.168.3.44.60172 > 10.0.0.3.8080: Flags [P.], cksum 0xedf1 (incorrect -> 0xaeb8), seq 4186712:4194904, ack 26, win 73, length 8192: HTTP
16:02:28.022413 IP (tos 0x0, ttl 63, id 58640, offset 0, flags [DF], proto TCP (6), length 40)
    10.0.0.3.8080 > 192.168.3.44.60172: Flags [.], cksum 0xbe93 (correct), seq 26, ack 4178520, win 12406, length 0
16:02:28.022486 IP (tos 0x0, ttl 63, id 58641, offset 0, flags [DF], proto TCP (6), length 40)
    10.0.0.3.8080 > 192.168.3.44.60172: Flags [.], cksum 0x9e93 (correct), seq 26, ack 4186712, win 12406, length 0
16:02:28.022523 IP (tos 0x0, ttl 63, id 58642, offset 0, flags [DF], proto TCP (6), length 40)
    10.0.0.3.8080 > 192.168.3.44.60172: Flags [.], cksum 0x7e93 (correct), seq 26, ack 4194904, win 12406, length 0
16:02:28.068999 IP (tos 0x0, ttl 63, id 58643, offset 0, flags [DF], proto TCP (6), length 336)
    10.0.0.3.8080 > 192.168.3.44.60172: Flags [P.], cksum 0xdb1b (correct), seq 26:322, ack 4194904, win 12406, length 296: HTTP, length: 296
	HTTP/1.1 200 OK
	Server: openresty/1.15.8.2
	Date: Fri, 04 Dec 2020 08:02:19 GMT
	Content-Length: 0
	Connection: keep-alive
	ETag: "b5cfa9d6c8febd618f91ac2843d50a1c"
	Accept-Ranges: bytes
	x-response-csp-component: proxy-raw
	
16:02:28.069643 IP (tos 0x0, ttl 64, id 997, offset 0, flags [DF], proto TCP (6), length 229)
    192.168.3.44.8080 > 10.0.0.13.37036: Flags [P.], cksum 0xceb8 (incorrect -> 0xca93), seq 1:190, ack 4195786, win 8233, length 189: HTTP, length: 189
	HTTP/1.1 201 
	Set-Cookie: JSESSIONID=E0B83CDF2538A5BC76A433B451599A56; Path=/; HttpOnly
	X-Application-Context: cos-convert:8080
	Content-Length: 0
	Date: Fri, 04 Dec 2020 08:02:28 GMT
	
16:02:28.107089 IP (tos 0x68, ttl 54, id 17397, offset 0, flags [DF], proto TCP (6), length 40)
    10.0.0.13.37036 > 192.168.3.44.8080: Flags [.], cksum 0x0218 (correct), seq 4195786, ack 190, win 237, length 0
16:02:28.107573 IP (tos 0x68, ttl 54, id 17398, offset 0, flags [DF], proto TCP (6), length 40)
    10.0.0.13.37036 > 192.168.3.44.8080: Flags [F.], cksum 0x0217 (correct), seq 4195786, ack 190, win 237, length 0
16:02:28.107940 IP (tos 0x0, ttl 64, id 998, offset 0, flags [DF], proto TCP (6), length 40)
    192.168.3.44.8080 > 10.0.0.13.37036: Flags [F.], cksum 0xcdfb (incorrect -> 0xe2d9), seq 190, ack 4195787, win 8233, length 0
16:02:28.108239 IP (tos 0x0, ttl 64, id 32693, offset 0, flags [DF], proto TCP (6), length 40)
    192.168.3.44.60172 > 10.0.0.3.8080: Flags [.], cksum 0xcdf1 (incorrect -> 0xad92), seq 4194904, ack 322, win 79, length 0
16:02:28.145332 IP (tos 0x68, ttl 54, id 17399, offset 0, flags [DF], proto TCP (6), length 40)
    10.0.0.13.37036 > 192.168.3.44.8080: Flags [.], cksum 0x0216 (correct), seq 4195787, ack 191, win 237, length 0
```


其中

```txt
10.0.0.13.37036 > 192.168.3.44.8080: Flags [S]
192.168.3.44.8080 > 10.0.0.13.37036: Flags [S.]
10.0.0.13.37036 > 192.168.3.44.8080: Flags [.]
```

代表三次握手链接数据标识

```txt
Flags [.]
Flags [P.]
```

代表数据开始传输中的标识


```txt
10.0.0.13.37036 > 192.168.3.44.8080: Flags [.]
10.0.0.13.37036 > 192.168.3.44.8080: Flags [F.]
192.168.3.44.8080 > 10.0.0.13.37036: Flags [F.]
192.168.3.44.60172 > 10.0.0.3.8080: Flags [.]
```

代表四次挥手完成数据传输
