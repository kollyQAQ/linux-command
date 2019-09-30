### 概述
sed 是一种在线编辑器，它一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为 “模式空间”（pattern space），接着用 sed 命令处理缓冲区中的内容，处理完成后，把缓冲区的内容**送往屏幕**。接着处理下一行，这样不断重复，直到文件末尾。**文件内容并没有改变，除非你使用重定向存储输出**。Sed 主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等

### 命令功能


### 命令格式
> sed [选项]... [动作]...

### 命令参数
#### 选项与参数：
- -n ：使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN 的数据一般都会被列出到终端上。但如果加上 -n 参数后，则只有经过sed 特殊处理的那一行(或者动作)才会被列出来
- -e ：直接在命令列模式上进行 sed 的动作编辑
- -f ：直接将 sed 的动作写在一个文件内， -f filename 则可以运行 filename 内的 sed 动作
- -r ：sed 的动作支持的是延伸型正规表示法的语法。(默认是基础正规表示法语法)
- -i ：直接修改读取的文件内容，而不是输出到终端
#### 动作说明： [n1[,n2]]function
n1, n2 ：不见得会存在，一般代表『选择进行动作的行数』，举例来说，如果我的动作是需要在 10 到 20 行之间进行的，则『 10,20[动作行为] 』
#### function：
- a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
- c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
- d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
- i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
- p ：列印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～
- s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是啦！

### 使用实例
#### 实例一：以行为单位的新增 / 删除
将 /etc/passwd 的内容列出并且列印行号，同时，请将第 2~5 行删除

**命令**
```shell
nl /etc/passwd | sed '2,5d'
```
**输出**
```shell
1 root:x:0:0:root:/root:/bin/bash
6 sync:x:5:0:sync:/sbin:/bin/sync
7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
.....(后面省略).....
```
**说明**
- sed 的动作为 '2,5d' ，那个 d 就是删除！因为 2-5 行给他删除了，所以显示的数据就没有 2-5 行罗～ 另外，注意一下，原本应该是要下达 sed -e 才对，没有 -e 也行啦！同时也要注意的是， sed 后面接的动作，请务必以 '' 两个单引号括住喔
- 只要删除第 2 行 
    ```shell
    nl /etc/passwd | sed '2d' 
    ```
- 要删除第 3 到最后一行
    ```shell
     nl /etc/passwd | sed '3,$d' 
    ```

#### 实例二：以行为单位的替换与显示
将第 2-5 行的内容取代成为『No 2-5 number』

**命令**
```shell
nl /etc/passwd | sed '2,5c No 2-5 number'
```
**输出**
```shell
1 root:x:0:0:root:/root:/bin/bash
No 2-5 number
6 sync:x:5:0:sync:/sbin:/bin/sync
.....(后面省略).....
```
仅列出 /etc/passwd 文件内的第 5-7 行

**命令**
```shell
nl /etc/passwd | sed -n '5,7p'
```
**输出**
```shell
5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
6 sync:x:5:0:sync:/sbin:/bin/sync
7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
.....(后面省略).....
```
**说明**
- 通过这个 sed 的以行为单位的显示功能，就能够将某一个文件内的某些行号选择出来显示

#### 实例三：数据的搜寻并显示
搜索 /etc/passwd 有 root 关键字的行

**命令**
```shell
nl /etc/passwd | sed -n '/root/p'
```
**输出**
```shell
1  root:x:0:0:root:/root:/bin/bash
....下面忽略 
```
**说明**
- 使用 `-n` 的时候将只打印包含模板的行，如果不加 `-n`，会输出所有行以及匹配行

#### 实例四：数据的搜寻并删除
删除 /etc/passwd 所有包含 root 的行，其他行输出

**命令**
```shell
nl /etc/passwd | sed  '/root/d'
```
**输出**
```shell
2  daemon:x:1:1:daemon:/usr/sbin:/bin/sh
3  bin:x:2:2:bin:/bin:/bin/sh
....下面忽略
#第一行的匹配root已经删除了
```

#### 数据的搜寻并替换
除了整行的处理模式之外， sed 还可以用行为单位进行部分数据的搜寻并取代。基本上 sed 的搜寻与替代的与 vi 相当的类似！他有点像这样：
```shell
sed 's/要被取代的字串/新的字串/g'
```

先观察原始信息，利用 /sbin/ifconfig 查询 IP
```shell
[root@www ~]# /sbin/ifconfig eth0
eth0 Link encap:Ethernet HWaddr 00:90:CC:A6:34:84
inet addr:192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0
inet6 addr: fe80::290:ccff:fea6:3484/64 Scope:Link
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
.....(以下省略).....
```
本机的 ip 是 192.168.1.100

将 IP 前面的部分予以删除
```shell
[root@www ~]# /sbin/ifconfig eth0 | grep 'inet addr' | sed 's/^.*addr://g'
192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0
```

接下来则是删除后续的部分，亦即： 192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0

将 IP 后面的部分予以删除
```shell
[root@www ~]# /sbin/ifconfig eth0 | grep 'inet addr' | sed 's/^.*addr://g' | sed 's/Bcast.*$//g'
192.168.1.100
```

#### 实例六：多点编辑
一条 sed 命令，删除 /etc/passwd 第三行到末尾的数据，并把 bash 替换为 blueshell

**命令**
```shell
nl /etc/passwd | sed -e '3,$d' -e 's/bash/blueshell/'
```
**输出**
```shell
1  root:x:0:0:root:/root:/bin/blueshell
2  daemon:x:1:1:daemon:/usr/sbin:/bin/sh
```
**说明**
- -e 表示多点编辑，第一个编辑命令删除 /etc/passwd 第三行到末尾的数据，第二条命令搜索 bash 替换为 blueshell

