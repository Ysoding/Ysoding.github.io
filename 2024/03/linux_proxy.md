# Linux

proxy url fromat

```
http://username:password@ip:port/
http://ip:port/
```

```sh
sudo vim /etc/environment
```

```

HTTPS_PROXY="http://username:password@ip:port/"
HTTP_PROXY="http://username:password@ip:port/"
NO_PROXY="192.168.0.141,192.168.0.0/24,localhost,127.0.0.0/8,::1"


https_proxy="http://username:password@ip:port/"
http_proxy="http://username:password@ip:port/"
no_proxy="192.168.0.141,192.168.0.0/24,localhost,127.0.0.0/8,::1"
```

### apt 设置代理

```sh
cat /etc/apt/apt.conf.d/80proxy

Acquire::http::proxy "http://ip:port";
Acquire::https::proxy "http://ip:port";
```
