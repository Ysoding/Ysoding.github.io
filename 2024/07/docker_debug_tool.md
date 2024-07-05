
## ctl/crictl

##  nsenter

```sh
nsenter -t <pid> -n <command>
```

由于容器内部经常没有一些工具的安装如ping/tcpdump/netstat/nslookup/ip route等等，每个容器一个个安装起来非常麻烦，
使用此工具则可以避免安装

pid查看一般容器ctl有提供inspect里面会有pid，如

```sh
docker inspect xxx | grep -i pid

crictl inspect xx | grep -i pid
```


## image的copy

使用ctl让image导出导入的时候，会出现`invalid tar header`，大概率是文件传输后不完整的问题。