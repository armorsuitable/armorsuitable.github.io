# 文本过滤操作

## awk 筛选文本

### ini 文件

```bash
awk -F '[ ]*=[ ]*' /replica_set/'{print $2}' ${DIR}/../ini/replicas.ini
```

代表过滤 replicas.ini 文件中的 replica_set 键

原文本内容：

```txt
replica_set=1
application_pro=true
```

## 文本字符替换


- -i 代表替换的字符模式
- 's/ ...  /g' 代表内容使用正则表达式并且全局替换

```bash
sed -i 's/{{csp_admin_secret_id}}/'${CSP_ID}'/g' yaml/converter-oam.yaml
```

其中 ${CSP_ID} 为执行命令获取到的 变量

示例

```bash
CSP_ID=$(kubectl get ingress | grep csp_id | awk {'print $1'})
```


## Less 文件查看

less -MN  查看文件时指定行号

```bash
less -MN application.log
```

- 输入 y  向上滚动一行
- 输入 b  向上滚动一屏
- 输入 enter 向下滚动一行
- 输入 blank space 向下滚动一屏


输入 / 查询文件
