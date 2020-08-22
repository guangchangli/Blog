# Nginx

## install

```
https://nginx.org/download/
环境 yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
## 解压
tar -zxvf nginx-1.9.9.tar.gz

##进入nginx目录
cd nginx-1.9.9
## 配置
./configure --prefix=/usr/local/nginx
# make
makemake install
```

```
启动
/sbin ./nginx 
停止
/sbin ./nginx -s stop
/sbin ./nginx -s quit 等到处理完停止
重启
/sbin ./nginx -s reload 重新加载配置文件
退出
/sbin ./nginx -s quit
```

## 开机启动

``vim /lib/systemd/system/nginx.service``

```shell
[Unit]
Description=nginx
After=network.target
  
[Service]
Type=forking
ExecStart=/path/to/nginx/sbin/nginx -c /path/to/nginx/conf/nginx.conf
ExecReload=/path/to/nginx/sbin/nginx -s reload
ExecStop=/path/to/nginx/sbin/nginx -s quit
PrivateTmp=true
  
[Install]
WantedBy=multi-user.target
```

```
Unit：服务说明
Description：服务描述
After：服务类别
Service：服务
Type：类型，forking代表后台运行
ExecStart：启动命令
ExecReload：重启命令
ExecStop：停止命令
PrivateTmp：true表示给服务分配独立的空间
Install：设置成多用户
```

```
systemctl enable nginx.service
```

```
# 设置开机自动启动
systemctl enable nginx.service

# 设置禁止开机自动启动
systemctl disable nginx.service

# 启动nginx服务
systemctl start nginx.service

# 重新加载nginx配置
systemctl reload nginx.service

# 停止nginx服务
systemctl stop nginx.service

# 查看nginx服务状态
systemctl status nginx.service
```

