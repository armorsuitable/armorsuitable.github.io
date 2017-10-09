---
layout: post
title:  "Linux-Unix Guide for Developers"
date:   2017-05-31 21:23:05 +0800
categories: jekyll update
---


### 1. Command /Shell 

    username@machine：~$ [command]  [parameters] [object]

``username``:  登录的用户名
``machine``：机器名/实例号

**~ :** 在Linux/Unix 系统中有特殊的含义（表示该用户的主目录）  
**$**: 表示操作用户不是管理员的标识符 （管理员为 #)  
**Current working Directory:**    当前工作的目录环境下 (一般默认在Ubuntu中显示所有的路径地址)  
**Absolute Directory :**  绝对路径，一般以根目录为起始目录（‘ /  ’）  
**Relative Directory :**  相对路径（在系统中存在特殊字符标识    ```..```  标识上级目录 ,  ``.`` 标识本目录）  
**一条命令的组成:  命令主体 [command]、命令参数 [parameters]、操作对象 [object]**
---

### 2.  Linux (Ubuntu) 文件系统结构
#### 2.1 文件系统树结构

在Linux系统中也可以存在多块硬盘（分区）用sda1, sda2...标识， **与Windows系统不同的是，Linux 只有一颗文件系统树**。

#### 2.2分区 和挂载点的概念 (Only One Tree) 

—Root -Directory      

 ``/``  在Linux文件系统中表示根目录 ，系统中所有的文件基于该目录之上。 

**默认根目录的权限是  d rwxr-xr-x   [ 7 5 5] 这里暂时不做解释**  
 
 ----``bin`` ：系统命令的存放目录，其中所有的文件均是二进制文件。（其中不再存储包或目录）  
 ----``sbin``：系统命令的存放目录，(二进制文件)。调用时存在管理员的高级权限方可执行  
 ----``sys``:    Linux 系统中一种基于虚拟内存的文件结构，与proc 功能类似。  
 ----``var``：系统可变数据量。（之后会详细讲解）  
 ----``usr``：注意╭(╯^╰)╮：该目录的缩写不是：User （而是Unix Shared Resource ），该目录之后会详细讲解。  
 ----``etc``：文件系统中的配置文件存放目录，其中配置文件必须是静态文件而非二进制文件。  
 ----``lib``：存放系统资源函数库的目录（功能繁杂，之后会详细讲解）  
 ----``home``：存放各个用户数据的目录，也是用户的主目录(root 用户除外)。  
 ----``root``：root 用户的主目录。  
 ----``dev``:  存储设备文件目录。  
 ----``boot``:存储Boot Loader静态文件，用于操作系统启动时。  


### 3. 文件模式

>文件/目录的模式，详细信息。

**文件一般在Linux中存在三种基本的权限：r (Read)、 w(Write) 、 x(Execute)**  

**Permission**  

**文件模式： Owner  Group  Others    —rw-rw-r—**     
**文件与目录的权限区别**：  
> File  :  
> **-R:   能够查看文件的内容          -W: 能够编辑文件 .                                      -X能够执行**
> Directory:  
> **-R: 能够查看目录中的文件      -W: 能够创建,删除,重命名目录中的文件,    -X:能够进入 . /**


例如下面的命令：

    lin@iZ94m06n3kyZ:~$ ls -l index.html
    -rw-rw-r-- 1 lin lin 0 Mar 29 12:00 index.html

---

    --| rw- | rw- | r--   1    lin    lin    0      Mar 29 12:00 index.html


第一个``-`` 标识文件类型  如果是目录则是 ``d``， 链接则是``l``
接下来是三个字母为一组的顺序形成一个标识主体
1. 首先是第一个 rw-  标识了 该文件的拥有者（Owner）对文件有 r 、w 权限。
2. 其次第二组字母标识了文件所属组中的成员拥有的权限 ，存在 r、w权限。
3. 最后一组字母标识了其他用户对文件的权限。
4.  最后一组字母后的 ``1`` 表示硬链接的数量。
5.  ``0``标识文件的大小。
6.  Mar 29 12:00  标识文件最后的修改时间。


#### 3.1  修改文件模式

查看用户所属组的全部信息： **id  -a  [USERNAME]**  
查看用户所属具体的组信息：  **id  -gn  [USERNAME]**  
添加用户至指定的组: **[Linux ] usermod -G -a Group  user**  [-a  参数必须添加 否则会脱离其他组]  
**[Mac  ]** **sudo dseditgroup -o edit  -a username  -t  user www**  
----

### 8. 网络状态查询  

    netstat -an | grep ESTABLISHED

    tcp        0      0 192.168.10.10:3306      192.168.10.1:62742      ESTABLISHED
    tcp        0      0 10.0.2.15:22            10.0.2.2:62715          ESTABLISHED
    tcp        0      0 192.168.10.10:3306      192.168.10.1:49952      ESTABLISHED
    udp6       0      0 ::1:36680               ::1:36680               ESTABLISHED


-----

### 9. 文件传输

**Unix / Linux SSH 文件传输**   

>从服务器上下载文件：

```
scp lin@172.16.76.129:/home/lin/index.php  /Users/elliot/local_dir [本地目录]  
```

>上传文件到服务器： 

```
scp /Users/elliot/apple.html lin@172.16.76.129:/home/lin/    [服务器目录] 
```

>从服务其下载整个目录： 


    scp -r lin@172.16.76.129:/home/lin/serverdir  [ 服务器目录]  /Users/elliot/ 


>上传本地目录到整个服务器：

```
scp -r /Users/elliot/Default-configs/  lin@172.16.76.129:/home/lin/serverdir  
```

**注：服务器的目录需要有权限 ，无论上传的文件还是目录最好写出绝对路径**

>Windows  Transfer: pscp

![Alt text](./Windows_transfer_remote_linux.png)


###附录：

Apache  ： PHP： ARP ： PCRE：  源码安装:

    APR - 
    elliot@Elliot-My-Kit:~/packages$ tar -jvxf apr-1.5.2.tar.bz2         
    elliot@Elliot-My-Kit:~/packages$ cd apr-1.5.2/  
    elliot@Elliot-My-Kit:~/packages/apr-1.5.2$ sudo ./configure   
    elliot@Elliot-My-Kit:~/packages/apr-1.5.2$ make  
    elliot@Elliot-My-Kit:~/packages/apr-1.5.2$ sudo make install  
    APR - UTIl 
    elliot@Elliot-My-Kit:~/packages$ cd apr-util-1.5.4/  
    elliot@Elliot-My-Kit:~/packages/apr-util-1.5.4$ ls  
    elliot@Elliot-My-Kit:/usr/local/apr$ cd /home/elliot/packages/apr-util-1.5.4  
    elliot@Elliot-My-Kit:~/packages/apr-util-1.5.4$ sudo ./configure --with-apr=/usr/local/apr  
    elliot@Elliot-My-Kit:~/packages/apr-util-1.5.4$ make  
    elliot@Elliot-My-Kit:~/packages/apr-util-1.5.4$ sudo make install  
    PCRE- 
    elliot@Elliot-My-Kit:~/packages$ tar -jvxf pcre-8.01.tar.bz2  
    elliot@Elliot-My-Kit:~/packages$ cd pcre-8.01/  
    elliot@Elliot-My-Kit:~/packages/pcre-8.01$ sudo ./configure   
    elliot@Elliot-My-Kit:~/packages/pcre-8.01$ sudo make  
    elliot@Elliot-My-Kit:~/packages/pcre-8.01$ sudo make install  
    elliot@Elliot-My-Kit:~/packages$ cd httpd-2.4.16/  
    elliot@Elliot-My-Kit:~/packages/httpd-2.4.16$ sudo ./configure   
    elliot@Elliot-My-Kit:~/packages/httpd-2.4.16$ sudo make   
    elliot@Elliot-My-Kit:~/packages/httpd-2.4.16$ sudo make install  



>Fix Q:
>error while loading shared libraries: libiconv.so.
>2: cannot open shared object file: 
>No such file or directory

http://blog.csdn.net/shanshu12/article/details/8003240
 
>AH00558: httpd.exe: Could not reliably determine the server's fully qualified do main name, using fe

http://blog.csdn.net/jaray/article/details/24395545

Debug

http://www.linuxidc.com/Linux/2012-09/70518.htm

PHP 

http://php.net/manual/zh/install.unix.apache2.php







                            
                        
                        