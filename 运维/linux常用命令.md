# vi
复制行：yy p
删除行：dd
覆盖：shift+r，ctrl+v，即先按大写R，然后粘贴，会从光标所在位置覆盖粘贴板中内容
文件内全部替换：:%s#abc#123#g (如文件内有#，可用/替换,:%s/abc/123/g) 把abc替换成123，注意开头的冒号，(或者: %s/str1/str2/g 用str2替换文件中所有的str1）
文件内局部替换：:20,30s#abc#123(如文件内有#，可用/替换,:%s/abc/123so/g) 把20行到30行内abc替换成123
剪切粘贴：按住v后移动方向选择文本，选择文本后按d剪切，按p粘贴。dd时剪切一行
搜索后 n 下一个关键字， shift+n上一个关键字
显示行号 :set number

# find
## 查文件夹里包含关键字的文件
find和grep结合使用
```shell
find . |xargs grep -ri '关键字' -l
```
参数说明：
* -r 逐层遍历查找，只找文件
* -i 忽略大小写
* -l 只显示文件名，一个文件多个匹配的话只显示一个，不加的话会显示匹配的文件内容，切会显示一个文件的所有匹配内容，例如查错误日志Error关键字，会造成控制台打印出n多内容

## 根据文件名查找文件
```shell
find . -name '123'
```
.表示当前目录

## 查当前目录大于800M的文件

```shell
 find . -type f -size +800M
```
 
-type参数    
* b 块设备
* d 目录
* c 字符设备
* p 管道
* l 符号链接
* **f 普通文件**

## 查指定天数之前的文件

```shell
find ./ -mtime +5 |xargs rm -rf
```
-mtime 指定天数
-mtime n : n为数字，意思为在n天之前的“一天之内”被更改过内容的文件
-mtime +n : 列出在n天之前（不含n天本身）被更改过内容的文件名
-mtime -n : 列出在n天之内（含n天本身）被更改过内容的文件名

最近访问时间 access time （-atime）
最近更改时间 modify time （-mtime） 
最近状态改动时间 change time（-ctime）

## 只查文件夹

-type d
d表示文件夹
常用的还有f表示文件


type参数

| 参数 | 文件类型 |
| --- | --- |
| b | 块设备 |
| d| 目录 | 
| c| 字符设备 | 
| p| 管道 | 
| l | 符号链接 | 
| f | 普通文件 | 


# scp
```shell
scp source user@ip:/path
```
指定端口的话用scp **-P**
mac本地scp到远程服务器可以结合**sshpass**命令做到免密，或者配置服务器间的信任

# touch
touch命令用于修改文件或者目录的时间属性，包括存取时间和更改时间。若文件不存在，系统会建立一个新的文件。
示例：

```shell
#修改文件的时间属性 
touch testfile
```
语法：

```shell
touch [-acfm][-d<日期时间>][-r<参考文件或目录>] [-t<日期时间>][--help][--version][文件或目录…]
```
参数说明：
* a 改变档案的读取时间记录。
* m 改变档案的修改时间记录。
* c 假如目的档案不存在，不会建立新的档案。与 --no-create 的效果一样。
* f 不使用，是为了与其他 unix 系统的相容性而保留。
* r 使用参考档的时间记录，与 --file 的效果一样。
* d 设定时间与日期，可以使用各种不同的格式。
* t 设定档案的时间记录，格式与 date 指令相同。
* --no-create 不会建立新档案。
* --help 列出指令格式。
* --version 列出版本讯息。


# grep

or过滤: 
grep ‘a\|b’ fileName   注意转义字符\
grep -E ‘a|b’ fileName 注意E大写
egrep ‘a|b’ fileName 等价与grep -E
grep -e a -e b fileName

and过滤
grep -E ‘a.*b' filename  ab顺序固定
grep -E ‘a.*b|b.*a' filename  通过或者实现ab顺序不固定
grep ‘a’ fileName | grep ‘b’ 通过管道符过滤两次

not过滤 
grep -v 'pattern1' filename 通过-v参数

前后指定行
-A 10 前10行
-B 10 后10行

列出匹配文件
grep -l '123' *.txt
注意-l如果是more后面管道符加grep的话只会输出(standard input)，需要直接使用grep命令

# du
计算出单个文件或者文件夹的磁盘空间占用
du -sh ./* 计算当前目录所有文件夹大小
**du -a | sort -n -r | head -n 10 当前文件夹下所有文件和文件夹最大的前十个**

# awk
用|分隔，打印第一个参数，注意print选项的大括号和单引号
awk -F "|" '{print $1}'
-F默认为空格
参数从$1开始，print时多个参数用逗号分隔

# sort
-u 去重
-n 按数字排序，默认是按字符串排序的
-r 倒序
-t -k sort模式是按文本的第一个字段排序的，如果要按其他字段排序，可以用-t指定分隔符，-k指定字段序号，-k从1开始

结合uniq使用可查出文本里出现次数最多的字符串
先sort排下序 然后 uniq -c去重并统计重复次数然后再sort排序
more info.2016-08-31.log | grep 'execute time'| awk '{print $10,$4,$5}'| sort | uniq -c |sort -u -n -r

# more
more命令，功能类似 cat ，cat命令是整个文件的内容从上到下显示在屏幕上。 more会以一页一页的显示方便使用者逐页阅读，而最基本的指令就是按空白键（space）就往下一页显示，按 b 键就会往回（back）一页显示，而且还有搜寻字串的功能 。more命令从前向后读取文件，因此在启动时就加载整个文件。

1. 命令格式
    more [-dlfpcsu] [-num] [+/pattern] [+linenum] 

2. 命令功能
    more命令和cat的功能一样都是查看文件里的内容，但有所不同的是more可以按页来查看文件的内容，还支持直接跳转行等功能。
3. 常用参数列表
     -num  一次显示的行数
     -d    在每屏的底部显示友好的提示信息
     -l    忽略 Ctrl+l （换页符）。如果没有给出这个选项，则more命令在显示了一个包含有 Ctrl+l 字符的行后将暂停显示，并等待接收命令。
     -f     计算行数时，以实际上的行数，而非自动换行过后的行数（有些单行字数太长的会被扩展为两行或两行以上）
     -p     显示下一屏之前先清屏。
     -c    从顶部清屏然后显示。
     -s    文件中连续的空白行压缩成一个空白行显示。
     -u    不显示下划线
     +/    先搜索字符串，然后从字符串之后显示
     +num  从第num行开始显示

4. 常用操作命令
    Enter    向下n行，需要定义。默认为1行
    Ctrl+F   向下滚动一屏
    空格键   向下滚动一屏
    Ctrl+B   返回上一屏
    =        输出当前行的行号
    ：f      输出文件名和当前行的行号
    v        调用vi编辑器
    !命令    调用Shell，并执行命令 
    q        退出more

# less

less 工具也是对文件或其它输出进行分页显示的工具，应该说是linux正统查看文件内容的工具，功能极其强大。less 的用法比起 more 更加的有弹性。在 more 的时候，我们并没有办法向前面翻， 只能往后面看，但若使用了 less 时，就可以使用 [pageup] [pagedown] 等按键的功能来往前往后翻看文件，更容易用来查看一个文件的内容！除此之外，在 less 里头可以拥有更多的搜索功能，不止可以向下搜，也可以向上搜。

1. 命令格式：
    less [参数]  文件 
2. 命令功能：
    less 与 more 类似，但使用 less 可以随意浏览文件，而 more 仅能向前移动，却不能向后移动，而且 less 在查看之前不会加载整个文件。
3. 命令参数：
    -b <缓冲区大小> 设置缓冲区的大小
    -e  当文件显示结束后，自动离开
    -f  强迫打开特殊文件，例如外围设备代号、目录和二进制文件
    -g  只标志最后搜索的关键词
    -i  忽略搜索时的大小写
    -m  显示类似more命令的百分比
    -N  显示每行的行号
    -o <文件名> 将less 输出的内容在指定文件中保存起来
    -Q  不使用警告音
    -s  显示连续空行为一行
    -S  行过长时间将超出部分舍弃
    -x <数字> 将“tab”键显示为规定的数字空格
    /字符串 向下搜索“字符串”
    ?字符串 向上搜索“字符串”
    n 重复前一个搜索（与 / 或 ? 有关）
    N 反向重复前一个搜索（与 / 或 ? 有关）
    b  向后翻一页
    d  向后翻半页</font>
    h  显示帮助界面
    Q  退出less 命令
    u  向前滚动半页
    y  向前滚动一行
    空格键 滚动一页
    回车键 滚动一行
    
# nc
NetCat，在网络工具中有“瑞士军刀”美誉，其有Windows和Linux的版本。因为它短小精悍（1.84版本也不过25k，旧版本或缩减版甚至更小）、功能实用，被设计为一个简单、可靠的网络工具，可通过TCP或UDP协议传输读写数据。同时，它还是一个网络应用Debug分析器，因为它可以根据需要创建各种不同类型的网络连接。

一、版本
通常的Linux发行版中都带有NetCat（简称nc），甚至在拯救模式光盘中也由busybox提供了简版的nc工具。但不同的版本，其参数的使用略有差异。
NetCat 官方地址：


引用[root@hatest1 ~]# cat /etc/asianux-release
Asianux release 2.0 (Trinity SP2)
[root@hatest1 ~]# cat /etc/redflag-release
Red Flag DC Server release 5.0 (Trinity SP2)
[root@hatest1 ~]# type -a nc
nc is /usr/bin/nc
[root@hatest1 ~]# rpm -q nc
nc-1.10-22

建议在使用前，先用man nc看看帮助。这里以红旗DC Server 5.0上的1.10版本进行简单说明。
假设两服务器信息：

server1: 192.168.10.10
server2: 192.168.10.11

二、常见使用
1、远程拷贝文件
从server1拷贝文件到server2上。需要先在server2上，，用nc激活监听，

server2上运行： nc -l 1234 > text.txt
server1上运行： nc 192.168.10.11 1234 < text.txt

注：server2上的监听要先打开

2、克隆硬盘或分区
操作与上面的拷贝是雷同的，只需要由dd获得硬盘或分区的数据，然后传输即可。
克隆硬盘或分区的操作，不应在已经mount的的系统上进行。所以，需要使用安装光盘引导后，进入拯救模式（或使用Knoppix工具光盘）启动系统后，在server2上进行类似的监听动作：

nc -l -p 1234 | dd of=/dev/sda
server1上执行传输，即可完成从server1克隆sda硬盘到server2的任务：
dd if=/dev/sda | nc192.168.10.11 1234
※ 完成上述工作的前提，是需要落实光盘的拯救模式支持服务器上的网卡，并正确配置IP。

3、端口扫描
可以执行：

`nc -v -w 2 192.168.10.11 -z 21-24`
nc: connect to 192.168.10.11 port 21 (tcp) failed: Connection refused
Connection to 192.168.10.11 22 port [tcp/ssh] succeeded!
nc: connect to 192.168.10.11 port 23 (tcp) failed: Connection refused
nc: connect to 192.168.10.11 port 24 (tcp) failed: Connection refused
-z后面跟的是要扫描的端口

4、保存Web页面
`while true; do nc -l -p 80 -q 1 < somepage.html; done`

5、模拟HTTP Headers

`nc 80`

```
GET / HTTP/1.1
Host: ispconfig.org
Referrer: mypage.com
User-Agent: my-browser

HTTP/1.1 200 OK
Date: Tue, 16 Dec 2008 07:23:24 GMT
Server: Apache/2.2.6 (Unix) DAV/2 mod_mono/1.2.1 mod_python/3.2.8 Python/2.4.3 mod_perl/2.0.2 Perl/v5.8.8
Set-Cookie: PHPSESSID=bbadorbvie1gn037iih6lrdg50; path=/
Expires: 0
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
Cache-Control: private, post-check=0, pre-check=0, max-age=0
Set-Cookie: oWn_sid=xRutAY; expires=Tue, 23-Dec-2008 07:23:24 GMT; path=/
Vary: Accept-Encoding
Transfer-Encoding: chunked
Content-Type: text/html
[......]
```

在nc命令后，输入红色部分的内容，然后按两次回车，即可从对方获得HTTP Headers内容。

6、聊天
nc还可以作为简单的字符下聊天工具使用，同样的，server2上需要启动监听：

server2上启动：# nc -lp 1234
server1上传输：# nc 192.168.10.11 1234

这样，双方就可以相互交流了。使用Ctrl+D正常退出。

7、传输目录
从server1拷贝nginx-0.6.34目录内容到server2上。需要先在server2上，用nc激活监听，

server2上运行：# nc -l 1234 |tar xzvf -
server1上运行：# tar czvf - nginx-0.6.34|nc 192.168.10.11 1234

8、用nc命名操作memcached

1）存储数据：printf “set key 0 10 6rnresultrn” |nc 192.168.10.11 11211
2）获取数据：printf “get keyrn” |nc 192.168.10.11 11211
3）删除数据：printf “delete keyrn” |nc 192.168.10.11 11211
4）查看状态：printf “statsrn” |nc 192.168.10.11 11211
5）模拟top命令查看状态：watch “echo stats” |nc 192.168.10.11 11211
6）清空缓存：printf “flush_allrn” |nc 192.168.10.11 11211 (小心操作，清空了缓存就没了）


# traceroute
路由跟踪
`traceroute www.baidu.com`

# useradd

```shell
useradd webadmin -m -g webadmin
```
需要先创建group，用groupadd命令
设置密码用passwd命令 passwd 用户名
参数：
    -c<备注>：加上备注文字。备注文字会保存在passwd的备注栏位中； 
    -d<登入目录>：指定用户登入时的启始目录；
    -D：变更预设值； 
    -e<有效期限>：指定帐号的有效期限； 
    -f<缓冲天数>：指定在密码过期后多少天即关闭该帐号； 
    -g<群组>：指定用户所属的群组； 
    -G<群组>：指定用户所属的附加群组； 
    -m：自动建立用户的登入目录； 
    -M：不要自动建立用户的登入目录； 
    -n：取消建立以用户名称为名的群组； 
    -r：建立系统帐号； 
    -s：指定用户登入后所使用的shell； 
    -u：指定用户id。
    
# ssh双机信任
`ssh-keygen  -t  rsa`
公钥添加到authorized_keys

<font color=#FF0000> 注意文件权限 .ssh目录的权限必须是700，同时本机的私钥和authorized_keys的权限必须设置成600</font>

# tar
-c: 建立压缩档案
-x：解压
-t：查看内容
-r：向压缩归档文件末尾追加文件
-u：更新原压缩包中的文件
这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

-z：有gzip属性的
-j：有bz2属性的
-Z：有compress属性的
-v：显示所有过程
-O：将文件解开到标准输出
下面的参数-f是必须的
-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。

`tar -cf all.tar *.jpg`
这条命令是将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。

`tar -rf all.tar *.gif`
这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。

`tar -uf all.tar logo.gif`
这条命令是更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。

`tar -tf all.tar`
这条命令是列出all.tar包中所有文件，-t是列出文件的意思

`tar -xf all.tar`
这条命令是解出all.tar包中所有文件，-x是解开的意思

压缩
tar -cvf jpg.tar *.jpg //将目录里所有jpg文件打包成tar.jpg 
tar -czf jpg.tar.gz *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
 tar -cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar -cZf jpg.tar.Z *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
rar a jpg.rar *.jpg //rar格式的压缩，需要先下载rar for linux
zip jpg.zip *.jpg //zip格式的压缩，需要先下载zip for linux
解压
tar -xvf file.tar //解压 tar包
tar -xzvf file.tar.gz //解压tar.gz
tar -xjvf file.tar.bz2   //解压 tar.bz2
tar -xZvf file.tar.Z   //解压tar.Z
unrar e file.rar //解压rar
unzip file.zip //解压zip
总结
1、*.tar 用 tar -xvf 解压
2、*.gz 用 gzip -d或者gunzip 解压
3、*.tar.gz和*.tgz 用 tar -xzf 解压
4、*.bz2 用 bzip2 -d或者用bunzip2 解压
5、*.tar.bz2用tar -xjf 解压
6、*.Z 用 uncompress 解压
7、*.tar.Z 用tar -xZf 解压
8、*.rar 用 unrar e解压
9、*.zip 用 unzip 解压

# top
各列含义
VIRT：virtual memory usage 虚拟内存
1、进程“需要的”虚拟内存大小，包括进程使用的库、代码、数据等
2、假如进程申请100m的内存，但实际只使用了10m，那么它会增长100m，而不是实际的使用量
RES：resident memory usage 常驻内存
1、进程当前使用的内存大小，但不包括swap out
2、包含其他进程的共享
3、如果申请100m的内存，实际使用10m，它只增长10m，与VIRT相反
4、关于库占用内存的情况，它只统计加载的库文件所占内存大小
SHR：shared memory 共享内存
1、除了自身进程的共享内存，也包括其他进程的共享内存
2、虽然进程只使用了几个共享库的函数，但它包含了整个共享库的大小
3、计算某个进程所占的物理内存大小公式：RES – SHR
4、swap out后，它将会降下来
DATA
1、数据占用的内存。如果top没有显示，按f键可以显示出来。
2、真正的该程序要求的数据空间，是真正在运行中要使用的。

top 运行中可以通过 top 的内部命令对进程的显示方式进行控制。内部命令如下：
s – 改变画面更新频率
l – 关闭或开启第一部分第一行 top 信息的表示
t – 关闭或开启第一部分第二行 Tasks 和第三行 Cpus 信息的表示
m – 关闭或开启第一部分第四行 Mem 和 第五行 Swap 信息的表示
N – 以 PID 的大小的顺序排列表示进程列表
P – 以 CPU 占用率大小的顺序排列进程列表
M – 以内存占用率大小的顺序排列进程列表
h – 显示帮助
n – 设置在进程列表所显示进程的数量
q – 退出 top
s – 改变画面更新周期

# head
查看文件的头几行

//n表示要查看的行数
head -n fileName 

//查看bill.json的第二行
head -2 bill.json

# runuser
使用其他用户运行命令
`runuser -l spark -c 'pwd'`

runuser --help
用法：runuser [选项]... [-] [用户 [参数]... ]
Change the effective user id and group id to that of USER.  Only session PAM
hooks are run, and there is no password prompt.  This command is useful only
when run as the root user.  If run as a non-root user without privilege
to set user ID, the command will fail as the binary is not setuid.
As runuser doesn't run auth and account PAM hooks, it runs with lower overhead
than su.

  -, -l, --login               make the shell a login shell, uses runuser-l
                               PAM file instead of default one
  -g --group=group             specify the primary group
  -G --supp-group=group        specify a supplemental group
  -c, --command=COMMAND        pass a single COMMAND to the shell with -c
  --session-command=COMMAND    pass a single COMMAND to the shell with -c
                               and do not create a new session
  -f, --fast                   pass -f to the shell (for csh or tcsh)
  -m, --preserve-environment   do not reset environment variables
  -p                           same as -m
  -s, --shell=SHELL            run SHELL if /etc/shells allows it
      --help        显示此帮助信息并退出
      --version        显示版本信息并退出

单独的"-"选项隐含了-l。如果不指定用户，则假设其为root。


# /bin/bash -c
通过-c参数来指定要执行的命令，通常用来在本机执行非本机的命令，如docker在宿主机执行容器命令：

```shell
docker exec -it $DOCKER_ID /bin/bash -c 'cd /packages/detectron && python tools/train.py'
```

# shell中"2>&1"含义

crontab中的定时任务配置：
```shell
*/2 * * * * root cd /opt/xxxx/test_S1/html/xxxx/admin; php index.php task testOne >/dev/null 2>&1
```
对于& 1 更准确的说应该是文件描述符 1,而1标识标准输出，stdout。
对于2 ，表示标准错误，stderr。
2>&1 的意思就是将标准错误重定向到标准输出。这里标准输出已经重定向到了 /dev/null。那么标准错误也会输出到/dev/null
* /dev/null 表示空设备文件
* 0 表示stdin标准输入
* 1 表示stdout标准输出
* 2 表示stderr标准错误

## command>a 2>a 与 command>a 2>&1的区别
command>a 2>&1这条命令，等价于command 1>a 2>&1可以理解为执行command产生的标准输入重定向到文件a中，标准错误也重定向到文件a中。那么是否就说command 1>a 2>&1等价于command 1>a 2>a呢。其实不是，command 1>a 2>&1与command 1>a 2>a还是有区别的，**区别就在于前者只打开一次文件a，后者会打开文件两次**，并导致stdout被stderr覆盖。&1的含义就可以理解为用标准输出的引用，引用的就是重定向标准输出产生打开的a。从IO效率上来讲，command 1>a 2>&1比command 1>a 2>a的效率更高。

# nohup和&的功效

使用&后台运行程序：
结果会输出到终端
使用Ctrl + C发送SIGINT信号，程序免疫
关闭session(关掉终端)发送SIGHUP信号，程序关闭

使用nohup运行程序：
结果默认会输出到nohup.out
使用Ctrl + C发送SIGINT信号，程序关闭
关闭session发送SIGHUP信号，程序免疫

平日线上经常使用nohup和&配合来启动程序：
同时免疫SIGINT和SIGHUP信号

同时，还有一个最佳实践：
**不要将信息输出到终端标准输出，标准错误输出，而要用日志组件将信息记录到日志里**