
<div style="font-size: 16px; line-height: 1.8; word-spacing: 2px; letter-spacing: 2px; font-family: 'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif; background-image: linear-gradient(90deg, rgba(50, 0, 0, 0.05) 3%, rgba(0, 0, 0, 0) 3%), linear-gradient(360deg, rgba(50, 0, 0, 0.05) 3%, rgba(0, 0, 0, 0) 3%); background-size: 20px 20px; background-position: center center;">

> __没有最好的 只有适合的

## 前言
Nginx是一个高性能Web服务器，使用之广泛，几乎所有的后端技术面试都可能涉及到Nginx的问题。

## Nginx优点
- 跨平台、配置简单、开源。
- 非阻塞、高并发连接；官方宣称5万并发。
- 内存消耗小。 
- 稳定性高，宕机的概率非常小。


## Nginx常用命令
- 启动 `nginx`
- 停止 `nginx -s stop` 或 `nginx -s quit`
- 重载配置 `nginx -s reload`(平滑重启)
- 重载指定配置文件 `nginx -c /usr/local/nginx/conf/nginx.conf`
- 查看nginx版本 `nginx -v`
- 检查配置文件是否正确 `nginx -t`
- 显示帮助信息 `nginx -h`

## Nginx配置

```conf
worker_processes  16; # 工作进程数 推荐为处理器内核数
worker_connections  65535; # 每个工作进程能并发处理（发起）的最大连接数（包含所有连接数）


## http 配置
http {

    server {
        listen       80; # 监听端口
        server_name  www.test.cn; # 允许域名

        location / {
            root   html; # 项目根目录
            index  index.html index.htm; # 访问根文件
        }
    }
}

```

## Nginx如何处理HTTP请求？

Master进程负责启动子进程，然后管理子进程！Nginx启动时Master使用epoll机制创建Socket监听。
客户端向Nginx发起连接，当客户端与Nginx进行三次握手，与nginx建立好一个连接后，子进程就会竞争accept这个新连接，某一个子进程会成功，得到这个建立好连接的Socket，进行相应的业务处理，然后返回响应数据。
 

##  Nginx为什么能实现高并发

使用了epoll，异步，非阻塞IO和大量细节处的优化。
WebSever是一个网络IO密集型的任务，大量时间都花费在网络传输上，非常适合使用异步、非阻塞IO。

## Nginx负载均衡

Nginx默认提供了3种负载均衡策略：

1. 轮询（默认）round_robin

> 每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

2. IP哈希 ip_hash

> 每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session共享的问题。
> 当然，实际场景下，一般不考虑使用ip_hash解决 session 共享。

3. 最少连接 least_conn

> 下一个请求将被分派到活动连接数量最少的服务器

通过Nginx插件，我们还可以引入 fair、url_hash 等负载均衡策略。


</div>