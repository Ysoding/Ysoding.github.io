## 场景

在192.168.0.53(router)机器上配置新的网段 172.100.1.0/24，并配置有一台机器172.100.1.21

- 给router安装一个USB网卡
- 用交换机连接起router的USB网卡和新主机


![Untitled](https://github.com/XmchxUp/picx-images-hosting/raw/master/20240514/Untitled.45i3j9nhp.webp)

## 操作

注意：这里NIC1（eno1）和NIC2（enx503eaa9ad449）是示例名称，你应该替换为你实际的网络接口名称。

### **配置NIC2**

```sh
// on route1 (192.168.0.53)
root@router1:~# service network_manager stop
root@router1:~# ifconfig NIC2 172.100.1.1 mtu 1500 up
```

**添加路由以供局域网访问，并启用IP转发**

```sh
// on route1 (192.168.0.53)
root@router1:~# route add -net 172.100.1.0 netmask 255.255.255.0 dev NIC2
root@router1:~# echo 1 > /proc/sys/net/ipv4/ip_forward
```

**在Router1上启用SNAT，以便内部局域网172.100.1.0/24可以访问互联网**

```sh
// on route1 (192.168.0.53)
root@router1:~# iptables -P INPUT ACCEPT
root@router1:~# iptables -P FORWARD ACCEPT
root@router1:~# iptables -t nat -A POSTROUTING -o NIC1 -j MASQUERADE
root@router1:~# iptables-save
```

配置完成后，路由表和iptables的状态如下：

```sh
root@router1:~# route
	Kernel IP routing table
	Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
	default         192.168.0.1     0.0.0.0         UG    100    0        0 eno1
	172.100.1.0     *               255.255.255.0   U     0      0        0 enx503eaa9ad449
	192.168.0.0     *               255.255.255.0   U     100    0        0 eno1
root@route1:~# iptables -t nat -L -nv
	..........
	Chain POSTROUTING (policy ACCEPT 3174 packets, 194K bytes)
	196 13787 MASQUERADE  all  --  *      enx503eaa9ad449  0.0.0.0/0            0.0.0.0/0
```

### 配置HOST

注意：这里eth0是示例名称，你应该替换为你实际的接口名称，例如enp0s31f6、eno2等。

```sh
// on 172.100.1.21 host
root@k8s-h-1:~# ifconfig eth0 172.100.1.21 mtu 1500 up
root@k8s-h-1:~# route add default gw 172.100.1.1
```

### 验证

在上述设置之后，从局域网172.100.1.0/24和192.168.1.0/24，主机电脑可以ping通局域网192.168.0.0/24

```sh
  root@k8s-h-1:~# ping 192.168.0.1 
    PING 192.168.0.1 (192.168.0.1) 56(84) bytes of data.
    64 bytes from 192.168.0.1: icmp_seq=1 ttl=64 time=0.514 ms
    64 bytes from 192.168.0.1: icmp_seq=2 ttl=64 time=0.529 ms
    ^C
```
