nginx 配置ssl 证书

1、安装依赖包

```
yum -y install gcc zlib zlib-level pcre-devel openssl openssl-devel
```

2、安装nginx

```
yum -y nginx
```

3、ssl配置

```
# /etc/nginx/conf.d/ssl.conf
server {
	listen 443;
	server_name your server name;
	ssl on;
	ssl_session_timeout 5m;
	# 证书路径
	ssl_certificate /etc/nginx/ssl证书文件;
	# 证书key
	ssl_certificate_key /etc/nginx/sslkey文件;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
	ssl_prefer_server_ciphers on;

	location / {
		# 转发到本地80端口
		proxy_pass http://127.0.0.1;
	}
}
```
