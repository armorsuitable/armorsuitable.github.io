# CURL 常见调试指南

## 请求参数

### HTTP 请求参数

- -i  代表获取响应端的头部数据
- -X 代表请求方法 默认是 GET
- -H 代表需要添加的请求头部信息
- -d 代表请求的数据提原始内容 一般用 `''` 包裹起来

示例：

```bash
curl -i -X POST 'http://xxxx.xxxx.xxx-m17.xxxx.fphere.cn' \ 
-H 'Content-Type:application/x-www-form-urlencode' \ 
-d 'Action=xxxxx&Bucket=test-1255000498&CosRegion=shanghai&EndDate=2020-11-16&Nonce=918486811&SecretId=xr*******c&StartDate=2020-11-12&Timestamp=1605604728&Version=2020-01-07&Signature=IcwX96nrrZGz7qTcW%2F505VH1MFI%3D'


HTTP/1.1 200 OK
Server: nginx
Date: Wed, 17 Mar 2021 21:26:34 GMT
Content-Type: application/json
Content-Length: 167
Connection: keep-alive
Access-Control-Allow-Credentials: true

{"Response":{"Error":{"Code":"MissingParameter","Message":"The request is missing a required parameter `Action`."},"RequestId":"db76077c-087d-4de4-a609-5cb54179ba00"}}
```


### 接收带有跳转301， 302 的请求

- -L  可接收跳转
- -v （verbose) 请求过程的详细信息 

示例

```bash
curl -i -L -v -X POST 'http://xxx.xxxx.xx.xxxx.xxx.cn' -H 'Content-Type:application/x-www-form-urlencode' -d 'Action=GetStatDays&Bucket=test-1255000498&CosRegion=shanghai&EndDate=2020-11-16&Nonce=918486811&SecretId=Axc******tXrqUps&StartDate=2020-11-12&Timestamp=1605604728&Version=2020-01-07&Signature=IcwX96nrrZGz7qTcW%2F505VH1MFI%3D'
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying 118.24.227.108...
* TCP_NODELAY set
* Connected to csp.api3.yf-m17.tcecqpoc.fsphere.cn (118.24.227.108) port 80 (#0)
> POST / HTTP/1.1
> Host: csp.api3.yf-m17.tcecqpoc.fsphere.cn
> User-Agent: curl/7.64.1
> Accept: */*
> Content-Type:application/x-www-form-urlencode
> Content-Length: 245
>
* upload completely sent off: 245 out of 245 bytes
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
< Server: nginx
Server: nginx
< Date: Wed, 17 Mar 2021 21:20:17 GMT
Date: Wed, 17 Mar 2021 21:20:17 GMT
< Content-Type: application/json
Content-Type: application/json
< Content-Length: 172
Content-Length: 172
< Connection: keep-alive
Connection: keep-alive
< Access-Control-Allow-Credentials: true
Access-Control-Allow-Credentials: true

<
* Connection #0 to host xxxx.xxx.yf-xxxx.xxxx.fsphere.cn left intact
{"Response":{"Error":{"Code":"AuthFailure.SignatureExpire","Message":"The signature is expired, please request again."},"RequestId":"a66a1f96-d971-407c-8ae6-996243342fca"}}
```


## 计算请求过程中的耗时

curl-format 文本

```txt
\n--------
\n
time_namelookup:  %{time_namelookup}\n
time_connect:  %{time_connect}\n
time_appconnect:  %{time_appconnect}\n
time_redirect:  %{time_redirect}\n
time_pretransfer:  %{time_pretransfer}\n
time_starttransfer:  %{time_starttransfer}\n
----------\n
time_total:  %{time_total}\n
```

将以上内容放到文本中, 请求时携带参数即可得到大致耗时

示例

```bash
curl -w "@curl-format" -i  -X POST 'http://xxxx.xxxx.yf-m17.xxxx.fphere.cn' -H 'Content-Type:application/x-www-form-urlencode' -d 'Action=GetStatDays&Bucket=test-1255000498&CosRegion=shanghai&EndDate=2020-11-16&Nonce=918486811&SecretId=AKIDKm******Ups&StartDate=2020-11-12&Timestamp=1605604728&Version=2020-01-07&Signature=IcwX96nrrZGz7qTcW%2F505VH1MFI%3D'
HTTP/1.1 200 OK
Server: nginx
Date: Wed, 17 Mar 2021 21:30:05 GMT
Content-Type: application/json
Content-Length: 172
Connection: keep-alive
Access-Control-Allow-Credentials: true

{"Response":{"Error":{"Code":"AuthFailure.SignatureExpire","Message":"The signature is expired, please request again."},"RequestId":"0624337c-d935-4431-a0bc-7b4baf074062"}}
----------
time_namelookup:  0.018956
time_connect:  0.019320
time_appconnect:  0.000000
time_redirect:  0.000000
time_pretransfer:  0.019367
time_starttransfer:  0.000000
----------
time_total:  0.102956
```

-g  GET 请求传递一个 key[item]=value 对


```bash
curl -i -g 'http://gz.xxx.xxx.xxxx.com/metric/dimensionlist?page=1&each=10&groupName=consumer_info_topic&apikey=qjegtK********lHvL0N5nZd&cond[appid]=1258638997&cond[instance_id]=ccccc-ccccc'

HTTP/1.1 200 OK
Server: nginx
Content-Type: application/json
Transfer-Encoding: chunked
Connection: close
X-Powered-By: PHP/5.6.29
Cache-Control: no-cache
Date: Tue, 25 May 2021 11:50:06 GMT
X-Frame-Options: SAMEORIGIN


{"count":18,"list":......","topicname":"dos\_apm\_jvm\_decode"}\]}
```

### 显示下载进度

```
curl  --progress-bar -Lf 
```
