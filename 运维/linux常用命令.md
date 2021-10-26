# vi
复制行：yy p
删除行：dd
覆盖：shift+r，ctrl+v，即先按大写R，然后粘贴，会从光标所在位置覆盖粘贴板中内容
文件内全部替换：:%s#abc#123#g (如文件内有#，可用/替换,:%s/abc/123/g) 把abc替换成123，注意开头的冒号，(或者: %s/str1/str2/g 用str2替换文件中所有的str1）
文件内局部替换：:20,30s#abc#123(如文件内有#，可用/替换,:%s/abc/123so/g) 把20行到30行内abc替换成123
剪切粘贴：按住v后移动方向选择文本，选择文本后按d剪切，按p粘贴。dd时剪切一行
搜索后 n 下一个关键字， shift+n上一个关键字
显示行号 :set number

## 移动光标

```
上:k nk:向上移动n行 9999k或gg可以移到第一行 G移到最后一行
下:j nj:向下移动n行
左:h nh:向左移动n列
右:l nl:向右移动n列

w：光标以单词向前移动 nw：光标向前移动n个单词 光标到单词的第一个字母上
b：与w相反
e: 光标以单词向前移动 ne：光标向前移动n个单词 光标到单词的最后一个字母上
ge: 与e相反

$:移动光标到行尾 n$:移动到第n行的行尾
0（Num）：移动光标到行首
^:移动光标到行首第一个非空字符上去

f:移动光标到当前行的字符a上，nf移动光标到当前行的第n个a字符上
F:相反

%:移动到与制匹配的括号上去（），{}，[]，<>等。

nG:移动到第n行上 G:到最后一行

CTRL＋G 得到当前光标在文件中的位置

向前翻页：CTRL+F
向下移动半屏：CTRL＋G

向后翻页：CTRL+B

到文件开头 gg
到文件结尾 shift+g
```

## 撤销和恢复
u撤销
ctrl+r恢复

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
`awk -F "|" '{print $1,$2}'`
-F默认为空格
参数从$1开始，print时多个参数用逗号分隔

# sort
-u 去重
-n 按数字排序，默认是按字符串排序的
-r 倒序
-t -k sort模式是按文本的第一个字段排序的，如果要按其他字段排序，可以用-t指定分隔符，-k指定字段序号，-k从1开始

结合uniq使用可查出文本里出现次数最多的字符串
先sort排下序 然后 uniq -c去重并统计重复次数然后再sort排序
`more info.2016-08-31.log | grep 'execute time'| awk '{print $10,$4,$5}'| sort | uniq -c |sort -u -n -r`

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

# mtr
全称my traceroute
和traceroute类似，多了个丢包率的统计

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
**注意2和>和&之间不能有空格**

# nohup和&的功能

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

# history
## 查看执行时间
系统变量里添加`export HISTTIMEFORMAT='%F %T'`
临时使用的话直接先执行这个命令，再执行history

# 查看网络使用情况
`sar -n DEV interval count`

其中，interval是统计时间间隔，以秒为单位；count是总共统计几次，如果为0就不断的统计直到 Ctrl+C 打断，否则执行count次就退出。
返回参数：
IFACE：LAN接口
rxpck/s：每秒钟接收的数据包
txpck/s：每秒钟发送的数据包
rxbyt/s：每秒钟接收的字节数，有些系统返回的是rxkB/s，即每秒接收多少kb
txbyt/s：每秒钟发送的字节数，有些系统返回的是txkB/s，即每秒发送多少kb


# lsof

## 参数
-a：列出打开文件存在的进程；
-c<进程名>：列出指定进程所打开的文件；
-g：列出GID号进程详情；
-d<文件号>：列出占用该文件号的进程；
+d<目录>：列出目录下被打开的文件；
+D<目录>：递归列出目录下被打开的文件；
-n<目录>：列出使用NFS的文件；
-i<条件>：列出符合条件的进程。（4、6、协议、:端口、 @ip ）
-p<进程号>：列出指定进程号所打开的文件；
-u：列出UID号进程详情；
-h：显示帮助信息；
-v：显示版本信息。

## 列
每行显示一个打开的文件，若不指定条件默认将显示所有进程打开的所有文件。lsof输出各列信息的意义如下：  
* COMMAND：进程的名称 
* PID：进程标识符 
* USER：进程所有者 
* FD：文件描述符，应用程序通过文件描述符识别该文件。如cwd、txt等 
* TYPE：文件类型，如DIR、REG等 
* DEVICE：指定磁盘的名称 
* SIZE：文件的大小 
* NODE：索引节点（文件在磁盘上的标识） 
* NAME：打开文件的确切名称 
其中FD 列中的文件描述符cwd 值表示应用程序的当前工作目录，这是该应用程序启动的目录，除非它本身对这个目录进行更改。txt 类型的文件是程序代码，如应用程序二进制文件本身或共享库，如上列表中显示的 /sbin/init 程序。其次数值表示应用程序的文件描述符，这是打开该文件时返回的一个整数。如上的最后一行文件/dev/initctl，其文件描述符为 10。u 表示该文件被打开并处于读取/写入模式，而不是只读(R) 或只写 (w) 模式。同时还有大写 的W 表示该应用程序具有对整个文件的写锁。该文件描述符用于确保每次只能打开一个应用程序实例。初始打开每个应用程序时，都具有三个文件描述符，0、1、2， 分别表示标准输入、输出和错误流。所以大多数应用程序所打开的文件的 FD 都是从 3 开始。  
Type : 文件和目录分别称为 REG 和 DIR（在 Solaris 中，称为 VREG 和 VDIR）。而CHR 和 BLK，分别表示字符和块设备； 或者 UNIX、FIFO 和 IPv4，分别表示 UNIX 域套接字、先进先出 (FIFO) 队列和网际协议 (IP) 套接字。

### 文件描述符列表

* cwd：表示current work dirctory，即：应用程序的当前工作目录，这是该应用程序启动的目录，除非它本身对这个目录进行更改
* txt：该类型的文件是程序代码，如应用程序二进制文件本身或共享库，如上列表中显示的 /sbin/init 程序
* lnn：library references (AIX);
* er：FD information error (see NAME column);
* jld：jail directory (FreeBSD);
* ltx：shared library text (code and data);
* mxx ：hex memory-mapped type number xx.
* m86：DOS Merge mapped file;
* mem：memory-mapped file;
* mmap：memory-mapped device;
* pd：parent directory;
* rtd：root directory;
* tr：kernel trace file (OpenBSD);
* v86  VP/ix mapped file;
* 0：表示标准输出
* 1：表示标准输入
* 2：表示标准错误

一般在标准输出、标准错误、标准输入后还跟着文件状态模式：

* u：表示该文件被打开并处于读取/写入模式。
* r：表示该文件被打开并处于只读模式。
* w：表示该文件被打开并处于。
* 空格：表示该文件的状态模式为unknow，且没有锁定。
* -：表示该文件的状态模式为unknow，且被锁定。

同时在文件状态模式后面，还跟着相关的锁：

* N：for a Solaris NFS lock of unknown type;
* r：for read lock on part of the file;
* R：for a read lock on the entire file;
* w：for a write lock on part of the file;（文件的部分写锁）
* W：for a write lock on the entire file;（整个文件的写锁）
* u：for a read and write lock of any length;
* U：for a lock of unknown type;
* x：for an SCO OpenServer Xenix lock on part      of the file;
* X：for an SCO OpenServer Xenix lock on the      entire file;
* space：if there is no lock.

### 文件类型

* DIR：表示目录。
* CHR：表示字符类型。
* BLK：块设备类型。
* UNIX： UNIX 域套接字。
* FIFO：先进先出 (FIFO) 队列。
* IPv4：网际协议 (IP) 套接字。
* DEVICE：指定磁盘的名称
* SIZE：文件的大小
* NODE：索引节点（文件在磁盘上的标识）
* NAME：打开文件的确切名称

## 常用命令

```shell
lsof abc.txt 显示开启文件abc.txt的进程
lsof -c abc 显示出以字母 abc 开头进程现在打开的文件
lsof -p 1234 列出进程号为1234的进程所打开的文件
lsof -g gname/gid 显示归属gname或gid的进程情况
lsof -u uname/uid 显示归属uname或uid的进程情况
lsof +d /usr/local/ 显示目录下被进程开启的文件
lsof +D /usr/local/ 同上，但是会搜索目录下的目录，时间较长
lsof -d 4 显示使用fd为4的进程
lsof -i 用以显示符合条件的进程情况
lsof -i[46] [protocol][@hostname|hostaddr][:service|port]
  46 --> IPv4 or IPv6
  protocol --> TCP or UDP
  hostname --> Internet host name
  hostaddr --> IPv4地址
  service --> /etc/service中的 service name (可以不止一个)
  port --> 端口号 (可以不止一个)
```

### 实例

```shell
lsof `which httpd` //那个进程在使用apache的可执行文件
lsof /etc/passwd //那个进程在占用/etc/passwd
lsof /dev/hda6 //那个进程在占用hda6
lsof /dev/cdrom //那个进程在占用光驱
lsof -c sendmail //查看sendmail进程的文件使用情况
lsof -c courier -u ^zahn //显示出那些文件被以courier打头的进程打开，但是并不属于用户zahn
lsof -p 30297 //显示那些文件被pid为30297的进程打开
lsof -D /tmp 显示所有在/tmp文件夹中打开的instance和文件的进程。但是symbol文件并不在列

lsof -u1000 //查看uid是100的用户的进程的文件使用情况
lsof -utony //查看用户tony的进程的文件使用情况
lsof -u^tony //查看不是用户tony的进程的文件使用情况(^是取反的意思)
lsof -i //显示所有打开的端口
lsof -i:80 //显示所有打开80端口的进程
lsof -i -U //显示所有打开的端口和UNIX domain文件
lsof -i UDP@[url]www.akadia.com:123 //显示那些进程打开了到www.akadia.com的UDP的123(ntp)端口的链接
lsof -i tcp@ohaha.ks.edu.tw:ftp -r //不断查看目前ftp连接的情况(-r，lsof会永远不断的执行，直到收到中断信号,+r，lsof会一直执行，直到没有档案被显示,缺省是15s刷新)
lsof -i tcp@ohaha.ks.edu.tw:ftp -n //lsof -n 不将IP转换为hostname，缺省是不加上-n参数
```

## 查看各进程产生的句柄数
`lsof -n|awk '{print $2}'|sort|uniq -c|sort -nr|more`

## 查看root用户打开的TXT文件
`lsof -a -u root -d txt `

# nload 查看网络使用情况
用途：用来即时监看网路状态和各IP所使用的频宽

nload 默认分为上下两块：

上半部分是：Incoming也就是进入网卡的流量，
下半部分是：Outgoing，也就是从这块网卡出去的流量，
每部分都有当前流量（Curr），
平均流量（Avg），
最小流量（Min），
最大流量（Max），
总和流量（Ttl）这几个部分，看起来还是蛮直观的。

nload默认的是eth0网卡，如果你想监测eth1网卡的流量使用nload eth1

-a：这个好像是全部数据的刷新时间周期，单位是秒，默认是300.
-i：进入网卡的流量图的显示比例最大值设置，默认10240 kBit/s.
-m：不显示流量图，只显示统计数据。
-o：出去网卡的流量图的显示比例最大值设置，默认10240 kBit/s.
-t：显示数据的刷新时间间隔，单位是毫秒，默认500。
-u：设置右边Curr、Avg、Min、Max的数据单位，默认是自动变的。注意大小写单位不同！

```
h|b|k|m|g h: auto, b: Bit/s, k: kBit/s, m: MBit/s etc.
H|B|K|M|G H: auto, B: Byte/s, K: kByte/s, M: MByte/s etc.

```
-U：设置右边Ttl的数据单位，默认是自动变的.注意大小写单位不同（与-u相同）！

Devices：自定义监控的网卡，默认是全部监控的，使用左右键切换。

如果只监控eth0命令使用，nload eth0
如果只监控eth1命令使用，nload eth1

nload eth0 ，可以查看第一网卡的流量情况，显示的是实时的流量图
nload -m 可以同时查看多个网卡的流量情况。

# 查看网络连接数

`netstat -nat|grep ESTABLISHED`

# curl
```shell
curl https://www.example.com
```
参数：

## -A 用户代理标头
-A参数指定客户端的用户代理标头，即User-Agent。curl 的默认用户代理字符串是curl/[version]。

```shell
curl -A 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.100 Safari/537.36' https://google.com
```
上面命令将User-Agent改成 Chrome 浏览器。

```shell
curl -A '' https://google.com
```
上面命令会移除User-Agent标头。
也可以通过-H参数直接指定标头，更改User-Agent。

```shell
curl -H 'User-Agent: php/1.0' https://google.com
```
## -b 向服务器发送Cookie
-b参数用来向服务器发送 Cookie。

```shell
curl -b 'foo=bar' https://google.com
```
上面命令会生成一个标头Cookie: foo=bar，向服务器发送一个名为foo、值为bar的 Cookie。

`$ curl -b 'foo1=bar;foo2=bar2' https://google.com`
上面命令发送两个 Cookie。

`$ curl -b cookies.txt https://www.google.com`
上面命令读取本地文件cookies.txt，里面是服务器设置的 Cookie（参见-c参数），将其发送到服务器。
## -c 将服务器设置的 Cookie 写入一个文件。

`$ curl -c cookies.txt https://www.google.com`
上面命令将服务器的 HTTP 回应所设置 Cookie 写入文本文件cookies.txt。

## -d 用于发送 POST 请求的数据体

`$ curl -d 'login=emma＆password=123'-X POST https://google.com/login`
或者
`$ curl -d 'login=emma' -d 'password=123' -X POST  https://google.com/login`
使用-d参数以后，HTTP 请求会自动加上标头Content-Type : application/x-www-form-urlencoded。并且会自动将请求转为 POST 方法，因此可以省略-X POST。
-d参数可以读取本地文本文件的数据，向服务器发送。

`$ curl -d '@data.txt' https://google.com/login`
上面命令读取data.txt文件的内容，作为数据体向服务器发送。
--data-urlencode
--data-urlencode参数等同于-d，发送 POST 请求的数据体，区别在于会自动将发送的数据进行 URL 编码。

`$ curl --data-urlencode 'comment=hello world' https://google.com/login`
上面代码中，发送的数据hello world之间有一个空格，需要进行 URL 编码。

## -e 设置 HTTP 的标头Referer，表示请求的来源。

`curl -e 'https://google.com?q=example' https://www.example.com`
上面命令将Referer标头设为https://google.com?q=example。
-H参数可以通过直接添加标头Referer，达到同样效果。

`curl -H 'Referer: https://google.com?q=example' https://www.example.com`

## -F 向服务器上传二进制文件。

`$ curl -F 'file=@photo.png' https://google.com/profile`
上面命令会给 HTTP 请求加上标头Content-Type: multipart/form-data，然后将文件photo.png作为file字段上传。
-F参数可以指定 MIME 类型。

`$ curl -F 'file=@photo.png;type=image/png' https://google.com/profile`
上面命令指定 MIME 类型为image/png，否则 curl 会把 MIME 类型设为application/octet-stream。
-F参数也可以指定文件名。

`$ curl -F 'file=@photo.png;filename=me.png' https://google.com/profile`
上面命令中，原始文件名为photo.png，但是服务器接收到的文件名为me.png。

## -G 构造 URL 的查询字符串。

`$ curl -G -d 'q=kitties' -d 'count=20' https://google.com/search`
上面命令会发出一个 GET 请求，实际请求的 URL 为https://google.com/search?q=kitties&count=20。如果省略--G，会发出一个 POST 请求。
如果数据需要 URL 编码，可以结合--data--urlencode参数。

`$ curl -G --data-urlencode 'comment=hello world' https://www.example.com`

## -H 添加 HTTP 请求的标头。

`$ curl -H 'Accept-Language: en-US' https://google.com`
上面命令添加 HTTP 标头Accept-Language: en-US。

`$ curl -H 'Accept-Language: en-US' -H 'Secret-Message: xyzzy' https://google.com`
上面命令添加两个 HTTP 标头。

`$ curl -d '{"login": "emma", "pass": "123"}' -H 'Content-Type: application/json' https://google.com/login`
上面命令添加 HTTP 请求的标头是Content-Type: application/json，然后用-d参数发送 JSON 数据。

## -i 打印出服务器回应的 HTTP 标头。

`$ curl -i https://www.example.com`
上面命令收到服务器回应后，先输出服务器回应的标头，然后空一行，再输出网页的源码。

## -I 向服务器发出 HEAD 请求，然会将服务器返回的 HTTP 标头打印出来。

`$ curl -I https://www.example.com`
上面命令输出服务器对 HEAD 请求的回应。
--head参数等同于-I。

`$ curl --head https://www.example.com`

## -k 指定跳过 SSL 检测。

`$ curl -k https://www.example.com`
上面命令不会检查服务器的 SSL 证书是否正确。

## -L 会让 HTTP 请求跟随服务器的重定向。curl 默认不跟随重定向。

`$ curl -L -d 'tweet=hi' https://api.twitter.com/tweet`

## --limit-rate 限制 HTTP 请求和回应的带宽，模拟慢网速的环境。

`$ curl --limit-rate 200k https://google.com`
上面命令将带宽限制在每秒 200K 字节。

## -o 将服务器的回应保存成文件，等同于wget命令。

`$ curl -o example.html https://www.example.com`
上面命令将www.example.com保存成example.html。

## -O 将服务器回应保存成文件，并将 URL 的最后部分当作文件名。

`$ curl -O https://www.example.com/foo/bar.html`
上面命令将服务器回应保存成文件，文件名为bar.html。

## -s 将不输出错误和进度信息。

`$ curl -s https://www.example.com`
上面命令一旦发生错误，不会显示错误信息。不发生错误的话，会正常显示运行结果。
如果想让 curl 不产生任何输出，可以使用下面的命令。

`$ curl -s -o /dev/null https://google.com`

## -S 指定只输出错误信息，通常与-s一起使用。

`$ curl -s -o /dev/null https://google.com`
上面命令没有任何输出，除非发生错误。

## -u 用来设置服务器认证的用户名和密码。

`$ curl -u 'bob:12345' https://google.com/login`
上面命令设置用户名为bob，密码为12345，然后将其转为 HTTP 标头Authorization: Basic Ym9iOjEyMzQ1。
curl 能够识别 URL 里面的用户名和密码。

`$ curl https://bob:12345@google.com/login`
上面命令能够识别 URL 里面的用户名和密码，将其转为上个例子里面的 HTTP 标头。

`$ curl -u 'bob' https://google.com/login`
上面命令只设置了用户名，执行后，curl 会提示用户输入密码。

## -v 输出通信的整个过程，用于调试。

`$ curl -v https://www.example.com`

## --trace 也可以用于调试，还会输出原始的二进制数据。

`$ curl --trace - https://www.example.com`

## -x 指定 HTTP 请求的代理。

`$ curl -x socks5://james:cats@myproxy.com:8080 https://www.example.com`
上面命令指定 HTTP 请求通过myproxy.com:8080的 socks5 代理发出。
如果没有指定代理协议，默认为 HTTP。

`$ curl -x james:cats@myproxy.com:8080 https://www.example.com`
上面命令中，请求的代理使用 HTTP 协议。

## -X 指定 HTTP 请求的方法。

`$ curl -X POST https://www.example.com`
上面命令对https://www.example.com发出 POST 请求。

## demo
### http post json
`curl http://www.baidu.com -X POST -d '{"login": "emma", "pass": "123"}' -H 'Content-Type: application/json'`

# split 切分文件
可以指定按行数分割和按字节大小分割两种模式。

## 按行数分割
`split -l 300 large_file.txt new_file_prefix`

加上-d，使用数字后缀；加上--verbose，显示分割进度

`split -l50000 -d large_file.txt part_ --verbose`

## 按字节大小分割

`split -b 10m large_file.log new_file_prefix`

## 参数列表
 
``` 
  -a, --suffix-length=N   generate suffixes of length N (default 2)            后缀名称的长度 (默认为2) 
      --additional-suffix=SUFFIX  append an additional SUFFIX to file names
  -b, --bytes=SIZE        put SIZE bytes per output file                       每个输出文件的字节大小
  -C, --line-bytes=SIZE   put at most SIZE bytes of records per output file    每个输出文件每行的最大字节大小
  -d                      use numeric suffixes starting at 0, not alphabetic   使用数字后缀代替字母后缀
      --numeric-suffixes[=FROM]  same as -d, but allow setting the start value
  -e, --elide-empty-files  do not generate empty output files with '-n'        不产生空的输出文件
      --filter=COMMAND    write to shell COMMAND; file name is $FILE           写入到shell命令行
  -l, --lines=NUMBER      put NUMBER lines/records per output file             设定每个输出文件的行数，默认行数是1000行
  -n, --number=CHUNKS     generate CHUNKS output files; see explanation below  产生chunks文件
  -t, --separator=SEP     use SEP instead of newline as the record separator;  使用新字符分割
                            '\0' (zero) specifies the NUL character
  -u, --unbuffered        immediately copy input to output with '-n r/...'     无需缓存
      --verbose           print a diagnostic just before each                  显示分割进度
                            output file is opened
      --help     display this help and exit                                    显示帮助信息
      --version  output version information and exit                           显示版本信息
```

# vmstat 监控工具

相比top命令，通过vmstat可以看到整个机器的 CPU，内存，IO的使用情况，而不是单单看到各个进程的CPU使用率和内存使用率。
`vmstat 2 10 -t`
参数说明：
2 每隔两秒统计一次
10 共统计10次，非必填，不填的话会一直统计，如果间隔时间和统计次数都不填的话，就只统计一次
-t 显示统计时间
-d 统计磁盘使用情况

```shell
vmstat 2 10 -t
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu----- -----timestamp-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st                 CST
 3  0      0 1142708 265428 1462096    0    0     0    94    0    0  5  0 95  1  0 2020-11-13 09:54:20
 2  0      0 1143164 265428 1462124    0    0     0   122 4732 2674 25  0 74  0  0 2020-11-13 09:54:22
 2  0      0 1141676 265428 1462132    0    0     0    86 5570 3094 25  0 75  0  0 2020-11-13 09:54:24
 2  0      0 1141768 265428 1462152    0    0     0    94 4371 2522 25  0 75  0  0 2020-11-13 09:54:26
 1  0      0 1141272 265428 1462152    0    0     0   106 4141 2444 24  0 76  0  0 2020-11-13 09:54:28
 2  0      0 1141272 265428 1462152    0    0     0    80 3998 2224 24  0 76  0  0 2020-11-13 09:54:30
 2  0      0 1140652 265428 1462152    0    0     0    84 4224 2279 26  0 74  0  0 2020-11-13 09:54:32
 1  0      0 1140652 265428 1462156    0    0     0    80 4115 2290 24  0 76  0  0 2020-11-13 09:54:34
 2  0      0 1140652 265428 1462156    0    0     0   134 4141 2296 25  0 75  0  0 2020-11-13 09:54:36
 2  0      0 1142604 265428 1462216    0    0     0    88 4348 2480 24  1 75  0  0 2020-11-13 09:54:38
```

# dstat 全能的监控工具

dstat命令是一个用来替换vmstat、iostat、netstat、nfsstat和ifstat这些命令的工具，是一个全能系统信息统计工具。
如果没有dstat命令，则需要进行下载安装。
参数列表:

```shell
  -c, --cpu              enable cpu stats
     -C 0,3,total           include cpu0, cpu3 and total
  -d, --disk             enable disk stats
     -D total,hda           include hda and total
  -g, --page             enable page stats
  -i, --int              enable interrupt stats
     -I 5,eth2              include int5 and interrupt used by eth2
  -l, --load             enable load stats
  -m, --mem              enable memory stats
  -n, --net              enable network stats
     -N eth1,total          include eth1 and total
  -p, --proc             enable process stats
  -r, --io               enable io stats (I/O requests completed)
  -s, --swap             enable swap stats
     -S swap1,total         include swap1 and total
  -t, --time             enable time/date output
  -T, --epoch            enable time counter (seconds since epoch)
  -y, --sys              enable system stats

  --aio                  enable aio stats
  --fs, --filesystem     enable fs stats
  --ipc                  enable ipc stats
  --lock                 enable lock stats
  --raw                  enable raw stats
  --socket               enable socket stats
  --tcp                  enable tcp stats
  --udp                  enable udp stats
  --unix                 enable unix stats
  --vm                   enable vm stats

  --plugin-name          enable plugins by plugin name (see manual)
  --list                 list all available plugins

  -a, --all              equals -cdngy (default)
  -f, --full             automatically expand -C, -D, -I, -N and -S lists
  -v, --vmstat           equals -pmgdsc -D total

  --bits                 force bits for values expressed in bytes
  --float                force float values on screen
  --integer              force integer values on screen

  --bw, --blackonwhite   change colors for white background terminal
  --nocolor              disable colors (implies --noupdate)
  --noheaders            disable repetitive headers
  --noupdate             disable intermediate updates
  --output file          write CSV output to file
  --profile              show profiling statistics when exiting dstat
```

![-w594](media/16052331203245.jpg)

# ps
## ps -ef各列含义
![-w811](media/16088899636651.jpg)

UID：用户id
PID：进程id
PPID：父进程id
C：cpu调度情况，CPU用于计算执行优先级的因子。
STIME：进程启动时间
TTY：启动线程的终端名，其中？表示该进程是守护进程 [参考文档](http://blog.chinaunix.net/uid-22746363-id-384008.html)
TIME：进程共占用CPU的时间
CMD：启动进程的命令

# date

```shell
Usage: date [OPTION]... [+FORMAT]
  or:  date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
Display the current time in the given FORMAT, or set the system date.

Mandatory arguments to long options are mandatory for short options too.
  -d, --date=STRING         显示时间由字符串描述，而不是'now'
  -f, --file=DATEFILE       like --date once for each line of DATEFILE
  -I[TIMESPEC], --iso-8601[=TIMESPEC]  output date/time in ISO 8601 format.
                            TIMESPEC='date' for date only (the default),
                            'hours', 'minutes', 'seconds', or 'ns' for date
                            and time to the indicated precision.
  -r, --reference=FILE      display the last modification time of FILE
  -R, --rfc-2822            output date and time in RFC 2822 format.
                            Example: Mon, 07 Aug 2006 12:34:56 -0600
      --rfc-3339=TIMESPEC   output date and time in RFC 3339 format.
                            TIMESPEC='date', 'seconds', or 'ns' for
                            date and time to the indicated precision.
                            Date and time components are separated by
                            a single space: 2006-08-07 12:34:56-06:00
  -s, --set=STRING          设置时间
  -u, --utc, --universal    print or set Coordinated Universal Time (UTC)
      --help     display this help and exit
      --version  output version information and exit

FORMAT controls the output.  Interpreted sequences are:
注意格式话串前面要加加号
  %%   a literal %
  %a   locale's abbreviated weekday name (e.g., Sun)
  %A   locale's full weekday name (e.g., Sunday)
  %b   locale's abbreviated month name (e.g., Jan)
  %B   locale's full month name (e.g., January)
  %c   locale's date and time (e.g., Thu Mar  3 23:05:25 2005)
  %C   century; like %Y, except omit last two digits (e.g., 20)
  %d   day of month (e.g., 01)
  %D   date; same as %m/%d/%y
  %e   day of month, space padded; same as %_d
  %F   full date; same as %Y-%m-%d
  %g   last two digits of year of ISO week number (see %G)
  %G   year of ISO week number (see %V); normally useful only with %V
  %h   same as %b
  %H   hour (00..23)
  %I   hour (01..12)
  %j   day of year (001..366)
  %k   hour, space padded ( 0..23); same as %_H
  %l   hour, space padded ( 1..12); same as %_I
  %m   month (01..12)
  %M   minute (00..59)
  %n   a newline
  %N   nanoseconds (000000000..999999999)
  %p   locale's equivalent of either AM or PM; blank if not known
  %P   like %p, but lower case
  %r   locale's 12-hour clock time (e.g., 11:11:04 PM)
  %R   24-hour hour and minute; same as %H:%M
  %s   从1970-01-01 00:00:00 UTC开始的秒数
  %S   second (00..60)
  %t   a tab
  %T   time; same as %H:%M:%S
  %u   day of week (1..7); 1 is Monday
  %U   week number of year, with Sunday as first day of week (00..53)
  %V   ISO week number, with Monday as first day of week (01..53)
  %w   day of week (0..6); 0 is Sunday
  %W   week number of year, with Monday as first day of week (00..53)
  %x   locale's date representation (e.g., 12/31/99)
  %X   locale's time representation (e.g., 23:13:48)
  %y   last two digits of year (00..99)
  %Y   year
  %z   +hhmm numeric time zone (e.g., -0400)
  %:z  +hh:mm numeric time zone (e.g., -04:00)
  %::z  +hh:mm:ss numeric time zone (e.g., -04:00:00)
  %:::z  numeric time zone with : to necessary precision (e.g., -04, +05:30)
  %Z   alphabetic time zone abbreviation (e.g., EDT)

By default, date pads numeric fields with zeroes.
The following optional flags may follow '%':

  -  (hyphen) do not pad the field
  _  (underscore) pad with spaces
  0  (zero) pad with zeros
  ^  use upper case if possible
  #  use opposite case if possible

After any flags comes an optional field width, as a decimal number;
then an optional modifier, which is either
E to use the locale's alternate representations if available, or
O to use the locale's alternate numeric symbols if available.

Examples:
Convert seconds since the epoch (1970-01-01 UTC) to a date
  $ date --date='@2147483647'

Show the time on the west coast of the US (use tzselect(1) to find TZ)
  $ TZ='America/Los_Angeles' date

Show the local time for 9AM next Friday on the west coast of the US
  $ date --date='TZ="America/Los_Angeles" 09:00 next Fri'

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
For complete documentation, run: info coreutils 'date invocation'

```

# 各种括号

## 小括号，圆括号（）

### 单小括号 ()
1. 命令组。括号中的命令将会新开一个子shell顺序执行，所以括号中的变量不能够被脚本余下的部分使用。括号中多个命令之间用分号隔开，最后一个命令可以没有分号，各命令和括号之间不必有空格。
2. 命令替换。等同于`cmd`，shell扫描一遍命令行，发现了$(cmd)结构，便将$(cmd)中的cmd执行一次，得到其标准输出，再将此输出放到原来命令。有些shell不支持，如tcsh。
3. 用于初始化数组。如：array=(a b c d)

### 双小括号 (( ))
1. 整数扩展。这种扩展计算是整数型的计算，不支持浮点型。((exp))结构扩展并计算一个算术表达式的值，如果表达式的结果为0，那么返回的退出状态码为1，或者 是"假"，而一个非零值的表达式所返回的退出状态码将为0，或者是"true"。若是逻辑判断，表达式exp为真则为1,假则为0。
2. 只要括号中的运算符、表达式符合C语言运算规则，都可用在`$((exp))`中，甚至是三目运算符。作不同进位(如二进制、八进制、十六进制)运算时，输出结果全都自动转化成了十进制。如：echo $((16#5f)) 结果为95 (16进位转十进制)
3. 单纯用 (( )) 也可重定义变量值，比如 a=5; ((a++)) 可将 $a 重定义为6
4. 常用于算术运算比较，双括号中的变量可以不使用`$`符号前缀。括号内支持多个表达式用逗号分开。 只要括号中的表达式符合C语言运算规则,比如可以直接使用for((i=0;i<5;i++)), 如果不使用双括号, 则为for i in \`seq 0 4\`或者`for i in {0..4}`。再如可以直接使用`if (($i<5))`, 如果不使用双括号, 则为`if [ $i -lt 5 ]`。

## 中括号，方括号[]

### 单中括号 []
1. bash 的内部命令，[和test是等同的。如果我们不用绝对路径指明，通常我们用的都是bash自带的命令。if/test结构中的左中括号是调用test的命令标识，右中括号是关闭条件判断的。这个命令把它的参数作为比较表达式或者作为文件测试，并且根据比较的结果来返回一个退出状态码。if/test结构中并不是必须右中括号，但是新版的Bash中要求必须这样。
2. Test和[]中可用的比较运算符只有==和!=，两者都是用于字符串比较的，不可用于整数比较，整数比较只能使用-eq，-gt这种形式。无论是字符串比较还是整数比较都不支持大于号小于号。如果实在想用，对于字符串比较可以使用转义形式，如果比较"ab"和"bc"：[ ab \< bc ]，结果为真，也就是返回状态为0。[ ]中的逻辑与和逻辑或使用-a 和-o 表示。
3. 字符范围。用作正则表达式的一部分，描述一个匹配的字符范围。作为test用途的中括号内不能使用正则。
4. 在一个array 结构的上下文中，中括号用来引用数组中每个元素的编号。

### 双中括号[[ ]]
1. [[是 bash 程序语言的关键字。并不是一个命令，[[ ]] 结构比[ ]结构更加通用。在[[和]]之间所有的字符都不会发生文件名扩展或者单词分割，但是会发生参数扩展和命令替换。
2. 支持字符串的模式匹配，使用=~操作符时甚至支持shell的正则表达式。字符串比较时可以把右边的作为一个模式，而不仅仅是一个字符串，比如[[ hello == hell? ]]，结果为真。[[ ]] 中匹配字符串或通配符，不需要引号。
3. 使用[[ ... ]]条件判断结构，而不是[ ... ]，能够防止脚本中的许多逻辑错误。比如，&&、||、<和> 操作符能够正常存在于[[ ]]条件判断结构中，但是如果出现在[ ]结构中的话，会报错。比如可以直接使用if [[ $a != 1 && $a != 2 ]], 如果不适用双括号, 则为if [ $a -ne 1] && [ $a != 2 ]或者if [ $a -ne 1 -a $a != 2 ]。
4. bash把双中括号中的表达式看作一个单独的元素，并返回一个退出状态码。

例子：

```shell
if ($i<5) 
if [ $i -lt 5 ] 
if [ $a -ne 1 -a $a != 2 ] 
if [ $a -ne 1] && [ $a != 2 ] 
if [[ $a != 1 && $a != 2 ]] 
for i in $(seq 0 4);do echo $i;done
for i in `seq 0 4`;do echo $i;done
for ((i=0;i<5;i++));do echo $i;done
for i in {0..4};do echo $i;done
```

## 大括号、花括号 {}

### 常规用法
1. 大括号拓展。(通配(globbing))将对大括号中的文件名做扩展。在大括号中，不允许有空白，除非这个空白被引用或转义。第一种：对大括号中的以逗号分割的文件列表进行拓展。如 touch {a,b}.txt 结果为a.txt b.txt。第二种：对大括号中以点点（..）分割的顺序文件列表起拓展作用，如：touch {a..d}.txt 结果为a.txt b.txt c.txt d.txt

    ```shell
    # ls {ex1,ex2}.sh 
    ex1.sh ex2.sh 
    # ls {ex{1..3},ex4}.sh 
    ex1.sh ex2.sh ex3.sh ex4.sh 
    # ls {ex[1-3],ex4}.sh 
    ex1.sh ex2.sh ex3.sh ex4.sh
    ```
2. 代码块，又被称为内部组，这个结构事实上创建了一个匿名函数 。与小括号中的命令不同，大括号内的命令不会新开一个子shell运行，即脚本余下部分仍可使用括号内变量。括号内的命令间用分号隔开，最后一个也必须有分号。{}的第一个命令和左括号之间必须要有一个空格。
　　
### 几种特殊的替换结构

`${var:-string},${var:+string},${var:=string},${var:?string}`

1. {var:-string}和${var:=string}:若变量var为空，则用在命令行中用string来替换${var:-string}，否则变量var不为空时，则用变量var的值来替换${var:-string}；对于${var:=string}的替换规则和${var:-string}是一样的，所不同之处是${var:=string}若var为空时，用string替换${var:=string}的同时，把string赋给变量var： ${var:=string}很常用的一种用法是，判断某个变量是否赋值，没有的话则给它赋上一个默认值。
2. ${var:+string}的替换规则和上面的相反，即只有当var不是空的时候才替换成string，若var为空时则不替换或者说是替换成变量 var的值，即空值。(因为变量var此时为空，所以这两种说法是等价的)
3. ${var:?string}替换规则为：若变量var不为空，则用变量var的值来替换${var:?string}；若变量var为空，则把string输出到标准错误中，并从脚本中退出。我们可利用此特性来检查是否设置了变量的值。
　　
　　补充扩展：在上面这五种替换结构中string不一定是常值的，可用另外一个变量的值或是一种命令的输出。
　　
### 四种模式匹配替换结构
模式匹配记忆方法：
\# 是去掉左边(在键盘上#在$之左边)
% 是去掉右边(在键盘上%在$之右边)
\#和%中的单一符号是最小匹配，两个相同符号是最大匹配。

`${var%pattern},${var%%pattern},${var#pattern},${var##pattern}`
1. 第一种模式：${variable%pattern}，这种模式时，shell在variable中查找，看它是否一给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最短的匹配模式
2. 第二种模式： ${variable%%pattern}，这种模式时，shell在variable中查找，看它是否一给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最长的匹配模式
3. 第三种模式：${variable#pattern} 这种模式时，shell在variable中查找，看它是否一给的模式pattern开始，如果是，就从命令行把variable中的内容去掉左边最短的匹配模式
4. 第四种模式： ${variable##pattern} 这种模式时，shell在variable中查找，看它是否一给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最长的匹配模式
5. 这四种模式中都不会改变variable的值，其中，只有在pattern中使用了\*匹配符号时，%和%%，#和##才有区别。结构中的pattern支持通配符，*表示零个或多个任意字符，?表示仅与一个任意字符匹配，[...]表示匹配中括号里面的字符，[!...]表示不匹配中括号里面的字符。

    ```shell
    # var=testcase 
    # echo $var 
    testcase 
    # echo ${var%s*e} 
    testca 
    # echo $var 
    testcase 
    # echo ${var%%s*e} 
    te 
    # echo ${var#?e} 
    stcase 
    # echo ${var##?e} 
    stcase 
    # echo ${var##*e}
    # echo ${var##*s} 
    e 
    # echo ${var##test} 
    case
    ```
### 字符串提取和替换

`${var:num},${var:num1:num2},${var/pattern/pattern},${var//pattern/pattern}`
1. 第一种模式：`${var:num}`，这种模式时，shell在var中提取第num个字符到末尾的所有字符。若num为正数，从左边0处开始；若num为负数，从右边开始提取字串，但必须使用在冒号后面加空格或一个数字或整个num加上括号，如`${var: -2}、${var:1-3}或${var:(-2)}`。
2. 第二种模式：`${var:num1:num2}`，num1是位置，num2是长度。表示从`$var字符串的第$num1`个位置开始提取长度为$num2的子串。不能为负数。
3. 第三种模式：${var/pattern/pattern}表示将var字符串的第一个匹配的pattern替换为另一个pattern。 
4. 第四种模式：${var//pattern/pattern}表示将var字符串中的所有能匹配的pattern替换为另一个pattern。

```shell
[root@centos ~]# var=/home/centos 
[root@centos ~]# echo $var 
/home/centos
[root@centos ~]# echo ${var:5} 
/centos
[root@centos ~]# echo ${var: -6} 
centos 
[root@centos ~]# echo ${var:(-6)} 
centos 
[root@centos ~]# echo ${var:1:4} 
home 
[root@centos ~]# echo ${var/o/h} 
/hhme/centos
[root@centos ~]# echo ${var//o/h} 
/hhme/cenths
```

## 符号$后的括号
1. ${a} 变量a的值, 在不引起歧义的情况下可以省略大括号。
2. `$(cmd)` 命令替换，和\`cmd\`效果相同，结果为shell命令cmd的输，过某些Shell版本不支持$()形式的命令替换, 如tcsh。
3. $((expression)) 和\`exprexpression\`效果相同, 计算数学表达式exp的数值, 其中exp只要符合C语言的运算规则即可, 甚至三目运算符和逻辑表达式都可以计算。

## 使用

### 多条命令执行
1. 单小括号，(cmd1;cmd2;cmd3) 新开一个子shell顺序执行命令cmd1,cmd2,cmd3, 各命令之间用分号隔开, 最后一个命令后可以没有分号。
2. 单大括号，{ cmd1;cmd2;cmd3;} 在当前shell顺序执行命令cmd1,cmd2,cmd3, 各命令之间用分号隔开, 最后一个命令后必须有分号, 第一条命令和左括号之间必须用空格隔开。

对{}和()而言, 括号中的重定向符只影响该条命令， 而括号外的重定向符影响到括号中的所有命令。

## 总结
以上所述是小编给大家介绍的shell中各种括号的作用()、(())、[]、[[]]、{}(推荐)，希望对大家有所帮助，如果大家有任何疑问请给我留言，小编会及时回复大家的。在此也非常感谢大家对脚本之家网站的支持！