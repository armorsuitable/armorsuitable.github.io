# 文件压缩与解压

## tar 压缩文件夹到指定目录

```bash
tar -zcvf /home/xahot.tar.gz /xahot
```

/xhaot 会指定所有的子目录

如果不需要一些目录，则使用 当前目录即可

```bash
tar -czvf ttx.tar.gz ttxdir
```

## Jar file extracting 

解压

```bash
jar xf file.jar
```

打包jar 

```bash
jar cvf program.jar -C path/to/classes .
```


zip 解压

```bash
unzip /path/to/app.jar -d /path/to/extracting/app
```

解压目录数据项到指定目录

## gz 文件解压


```bash
gzip -d package.tar.gz
```
