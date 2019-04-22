## 动态脚本
### if 语法
 
```shell
#如果请求里token参数为空
if ($arg_token = '') {
    set $token  para; 
}
```

不支持else语法，多条件只能挨个写if
注意括号前后必须有空格

#### 可用于if的全局变量

```shell
$args ： #这个变量等于请求行中的参数，同$query_string
$content_length ： 请求头中的Content-length字段。
$content_type ： 请求头中的Content-Type字段。
$document_root ： 当前请求在root指令中指定的值。
$host ： 请求主机头字段，否则为服务器名称。
$http_user_agent ： 客户端agent信息
$http_cookie ： 客户端cookie信息
$limit_rate ： 这个变量可以限制连接速率。
$request_method ： 客户端请求的动作，通常为GET或POST。
$remote_addr ： 客户端的IP地址。
$remote_port ： 客户端的端口。
$remote_user ： 已经经过Auth Basic Module验证的用户名。
$request_filename ： 当前请求的文件路径，由root或alias指令与URI请求生成。
$scheme ： HTTP方法（如http，https）。
$server_protocol ： 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
$server_addr ： 服务器地址，在完成一次系统调用后可以确定这个值。
$server_name ： 服务器名称。
$server_port ： 请求到达服务器的端口号。
$request_uri ： 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
$uri ： 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
$document_uri ： 与$uri相同。
```

### 获取请求参数

```shell
#获取token参数
$arg_token 
#所有请求参数
$query_string
#获取请求头参数
$http_token
```

### 获取请求地址

```shell
#请求的文件和路径，不包括“?”或者“#”之后的东西
$uri
#$request_uri则是请求的整个字符串，包含了后面的query_string
$request_uri

```
## 错误页设置
三种方式
1. 设置error_page参数
    可设置全局的，也可设置具体location的
    
    ```
    error_page 404 = https://www.baodu.com;
    ```
1. 设置error_page指向的location，然后配置location
    
    ```
    error_page  500 502 503 504 /50x.html;
    location = /50x.html {
        root  html;
    }
    ```
2. 设置try_files
    try_files指定一个本地文件链，从第一个文件开始匹配，直到找到能正常访问的文件
    
    ```shell
    # 先找uri这个变量对应的文件，然后uri这个文件夹，然后首页
    try_files $uri $uri/ /index.php?$args;
    ```
    
    注意try_files**只能对应本地文件**，无法指向远程文件


**注意设置错误页需要在配置文件中添加**：

```
proxy_intercept_errors on;
```
来开启nginx的错误代理，否则不生效
    
## nginx指令
    
### try_files
try_files指定一个调用链，从第一个开始请求，直到请求到一个能正常访问的页面为止
    
```
try_files $uri $uri/ /index.html?$query_string; 
```
先请求uri（uri参数在nginx里指的是请求路径，不带参数），然后请求uri文件夹，最后请求index.html并拼接完整参数

### upstream

```shell
upstream test {
    server 172.16.0.53:8888 max_fails=3 fail_timeout=10s;
    check interval=5000 rise=2 fall=3 timeout=1000;
}
```

### location

```shell
location ^~ /test/ {
        proxy_pass http://test/;
    }
```

#### 绝对路径和相对路径
如果在proxy_pass后面的url加/，表示绝对根路径，会把匹配的path吃掉；如果没有/，表示相对路径，把匹配的路径部分也给代理过去

### rewrite
```shell
rewrite 匹配规则 重定向地址;
```
* rewrite指令后面不能直接跟`$`变量，变通方法是常量后面拼变量，如`http://$url`
* 如果有多条rewrite规则，不同规则通过if匹配，此时rewrite最后需要加上break，否则还会执行下一条rewrite ps:试了下多个if里加break，貌似不生效，后来使用了if里定义变量，最外面rewrite时使用变量值的方式
    
```
        if ($arg_version != '2') {
            set $rw lucky.peopletech.cn/wap-news-mi/#/normal/$arg_id?lang=$arg_lang;
        }
        if ($arg_version = '2') {
            set $rw lucky.peopletech.cn/oss-mi/$arg_env/$arg_mibusinessId/$arg_id.html;
        }
        rewrite "^/(.*)$" http://$rw;
```