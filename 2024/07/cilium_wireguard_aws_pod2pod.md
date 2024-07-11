## 场景问题

环境cilium(wireguard) + kubeedge，云主机为Master, 家里的主机为Edge Node。

问题: 就是创建个busybox ds, 云上的pod与本地主机中的pod ping不通。

正常话的，先从本地主机pod 云ping 云上的应该是Ok的，再反过来也是行的。


cilium version: v1.15.3

> 注意下面出现的日志可能不一致，因为做了很多尝试，没保留。

<details>
<summary>问题日志</summary>

```sh
root@hcss-ecs-0616:~# sudo kubectl get pod -A -o wide
NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE     IP               NODE            NOMINATED NODE   READINESS GATES
default       busybox-79trp                           1/1     Running   0          9m53s   10.0.1.248       ultraman        <none>           <none>
default       busybox-zwbdz                           1/1     Running   0          9m53s   10.0.0.207       hcss-ecs-0616   <none>           <none>
kube-system   cilium-hvnfd                            1/1     Running   0          160m    192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   cilium-kubeedge-hmwc7                   1/1     Running   0          12m     192.168.18.228   ultraman        <none>           <none>
kube-system   cilium-operator-fdf6bc9f4-4hxkc         1/1     Running   0          176m    192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   coredns-5bbd96d687-45k4z                1/1     Running   0          3h3m    10.0.0.234       hcss-ecs-0616   <none>           <none>
kube-system   coredns-5bbd96d687-qnnzb                1/1     Running   0          3h3m    10.0.0.6         hcss-ecs-0616   <none>           <none>
kube-system   etcd-hcss-ecs-0616                      1/1     Running   2          3h3m    192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   kube-apiserver-hcss-ecs-0616            1/1     Running   3          3h3m    192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   kube-controller-manager-hcss-ecs-0616   1/1     Running   4          3h3m    192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   kube-proxy-hfm8x                        1/1     Running   0          3h3m    192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   kube-proxy-pgb85                        1/1     Running   0          17m     192.168.18.228   ultraman        <none>           <none>
kube-system   kube-scheduler-hcss-ecs-0616            1/1     Running   4          3h3m    192.168.10.106   hcss-ecs-0616   <none>           <none>
kubeedge      cloudcore-5795465cb6-8pthm              1/1     Running   0          112m    192.168.10.106   hcss-ecs-0616   <none>           <none>
kubeedge      edge-eclipse-mosquitto-bj6c6            1/1     Running   0          11m     192.168.18.228   ultraman        <none>           <none>

ultraman@ultraman ~ % sudo crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock exec -it 0e081ca21cf34 sh
/ # 
/ # ping 10.0.0.207
PING 10.0.0.207 (10.0.0.207): 56 data bytes
^C
--- 10.0.0.207 ping statistics ---
1 packets transmitted, 0 packets received, 100% packet loss
/ # exit


root@hcss-ecs-0616:~# sudo kubectl exec  -it busybox-zwbdz -- sh
/ # ping 10.0.1.248
PING 10.0.1.248 (10.0.1.248): 56 data bytes
^C
--- 10.0.1.248 ping statistics ---
2 packets transmitted, 0 packets received, 100% packet loss


local
ultraman@ultraman ~ % sudo wg                   
interface: cilium_wg0
  public key: lvb/LAj3FWIFledxbKxbnF/XIWOxc3hKq0HmWLLRhVE=
  private key: (hidden)
  listening port: 51871
  fwmark: 0x1e00

peer: Re7Qodot5YwbL84oqzm2954Kz21Qbpg5+BqoPjwYe1Y=
  endpoint: 192.168.10.106:51871
  allowed ips: 192.168.10.106/32, 10.0.0.234/32, 10.0.0.6/32, 10.0.0.117/32, 10.0.0.206/32, 10.0.0.207/32


云上
sudo wg
interface: cilium_wg0
  public key: Re7Qodot5YwbL84oqzm2954Kz21Qbpg5+BqoPjwYe1Y=
  private key: (hidden)
  listening port: 51871
  fwmark: 0x1e00

peer: lvb/LAj3FWIFledxbKxbnF/XIWOxc3hKq0HmWLLRhVE=
  endpoint: 192.168.18.228:51871
  allowed ips: 192.168.18.228/32, 10.0.1.97/32, 10.0.1.191/32, 10.0.1.248/32
  transfer: 0 B received, 31.22 KiB sent

```
</details>

## 排查

### Fail
主要先是看到上面的日志中，wireguard配置没成功，应该成功的话会，两端的transfer 中的 received bytes不会是0。

然后想到怎么debug wireguard，看wireguard相关的日志。

[Debug WireGuard Linux kernel module · GitHub](https://gist.github.com/artizirk/5bc87e345f850a8a0724929e0436ef84)  

```sh
echo 'module wireguard +p' | sudo tee /sys/kernel/debug/dynamic_debug/control


dmesg | grep cilium_wg0

[ 9022.320981] device cilium_wg0 entered promiscuous mode

[ 9053.653508] device cilium_wg0 left promiscuous mode

[ 9815.431555] device cilium_wg0 entered promiscuous mode

[ 9823.605600] device cilium_wg0 left promiscuous mode

[11836.540316] wireguard: cilium_wg0: No peer has allowed IPs matching 113.44.78.147

[11836.548732] wireguard: cilium_wg0: No peer has allowed IPs matching 113.44.78.147

[11837.579799] wireguard: cilium_wg0: No peer has allowed IPs matching 113.44.78.147

[11956.541336] wireguard: cilium_wg0: No peer has allowed IPs matching 113.44.78.147
```

发现local这端一直说没有allowed 云上的External IP。

后面尝试下手动给allowed ip加上113.44.78.147

```sh
sudo wg set cilium_wg0 peer eKl8SvbgtutrlszzcM0wvXj9e2xm0eibPw9Pzo3xVjg= allowed-ips 192.168.10.106/32,10.0.0.230/32,10.0.0.15/32,10.0.0.35/32,10.0.0.126/32,113.44.78.147/32
```

然后local和云主机就建立连接成功了，云上的wireguard peer endpoint也变成local的出口地址了180.172.97.88。


local 
```  
k8s@k8s-109:~$ sudo wg
interface: cilium_wg0
  public key: Izy5W1viHwkkCL1gRuTO5iINCeFcujXg+lgALIpBWR4=
  private key: (hidden)
  listening port: 51871
  fwmark: 0x1e00

peer: eKl8SvbgtutrlszzcM0wvXj9e2xm0eibPw9Pzo3xVjg=
  endpoint: 192.168.10.106:51871
  allowed ips: 192.168.10.106/32, 10.0.0.230/32, 10.0.0.15/32, 10.0.0.35/32, 10.0.0.126/32, 113.44.78.147/32
  latest handshake: 4 seconds ago
  transfer: 92 B received, 292 B sent
```  

云
```  
root@hcss-ecs-0616:~# sudo wg

interface: cilium_wg0
  public key: eKl8SvbgtutrlszzcM0wvXj9e2xm0eibPw9Pzo3xVjg=
  private key: (hidden)
  listening port: 51871
  fwmark: 0x1e00

peer: Izy5W1viHwkkCL1gRuTO5iINCeFcujXg+lgALIpBWR4=
  endpoint: 180.172.97.88:51871
  allowed ips: 192.168.10.105/32, 10.0.1.231/32, 10.0.1.34/32
  latest handshake: 10 seconds ago
  transfer: 292 B received, 124 B sent
```


好景不长，手动设置，一会儿会被立刻更新掉配置。想了下可以改下代码，自己编译个cilium版本，提并到dockerhub上，然后部署的时候改cilium的image地址。

找到cilium代码中的wireguard配置逻辑，

pkg/wireguard/agent.go
更新逻辑再这
```go
func (a *Agent) UpdatePeer(nodeName, pubKeyHex string, nodeIPv4, nodeIPv6 net.IP) error {
  ...
  // 这是初始化的
  if allowedIPs == nil {
		// (Re-)Initialize the allowedIPs list by querying the IPCache. The
		// allowedIPs will be updated by OnIPIdentityCacheChange after this
		// function returns.
		var lookupIPv4, lookupIPv6 net.IP
		if option.Config.EnableIPv4 && nodeIPv4 != nil {
			lookupIPv4 = nodeIPv4
      // 这里加个if如果nodeIPv4不是云的internal ip，就将云的External ip放到allowedIPs里
      internalIP := net.ParseIP("192.168.0.123")
      exteranlIP := net.ParseIP("x.x.x.x")
      if internalIP.Equal(nodeIPv4) {
       allowedIPs = append(allowedIPs, net.IPNet{
        IP:   externalIP,
        Mask: net.CIDRMask(net.IPv4len*8, net.IPv4len*8),
        }) 
      }
      allowedIPs = append(allowedIPs, net.IPNet{
        IP:   nodeIPv4,
        Mask: net.CIDRMask(net.IPv4len*8, net.IPv4len*8),
      })
		}
		if option.Config.EnableIPv6 && nodeIPv6 != nil {
			lookupIPv6 = nodeIPv6
      allowedIPs = append(allowedIPs, net.IPNet{
        IP:   nodeIPv6,
        Mask: net.CIDRMask(net.IPv6len*8, net.IPv6len*8),
      })
		}
  }
}
```

build失败发现网络问题，没有设置代理。

Makefile.docker:98
```docker
		--build-arg http_proxy="" \
		--build-arg https_proxy="" \
```



my-build.sh
```sh
#!/bin/bash

#export DOCKER_BUILDKIT=1
export DOCKER_BUILDX=1

export DOCKER_REGISTRY=docker.io
export DOCKER_DEV_ACCOUNT=xmchx

export https_proxy=
export http_proxy=

#make docker-images-all
make docker-cilium-image
```

```
sh ./my-build.sh
docker push xmchx/cilium:latest
```


至此修改过后的版本搞好了。

不久前刚好把docker hub给封了，用不了。

想了下在云上还是直接手动的上传image然后import。

```sh
sudo ctr image export c-cilium.tar docker.io/xmchx/cilium:latest

scp

注意要指定namespace k8s.io，k8s会将镜像放到这个namespace里
sudo ctr -n k8s.io image import c-cilium.tar 

```


配置cilium的image地址

```sh
kubectl edit ds -n kube-system cilium

// 这里简单的将所有配置中的imagen改成自己的
image: docker.io/cilium/cilium:latest@sha256:xxxxxxxxxxxxxx
```


恩，wireguard目测配置ok，虽然但是，依然Ping不通，野路子放弃。

也一通流量分析过
![Untitled](https://github.com/XmchxUp/picx-images-hosting/raw/master/20240711/Untitled.67xcgtyeq3.webp)



### Success

先研究下整体结构是咋样的，最找的版本有讲[this](https://cilium.io/blog/2021/05/20/cilium-110/)，然后对比看当前配置发现不致，估计整个配置方式改变了，底层也不懂啊，就用了老版本试试。

```sh
cilium install --set encryption.enabled=true --set encryption.type=wireguard --set encryption.wireguard.persistentKeepalive=20  --set l7Proxy=false --version 1.13.0-rc5
```

设置l7Proxy=false的原因
```sh
level=info msg="Auto-disabling \"enable-bpf-clock-probe\" feature since KERNEL_HZ cannot be determined" error="Cannot probe CONFIG_HZ" subsys=daemon    
level=info msg="Using autogenerated IPv4 allocation range" subsys=node v4Prefix=10.236.0.0/16
level=info msg="Initializing daemon" subsys=daemon
level=fatal msg="Wireguard (--enable-wireguard) is not compatible with L7 proxy (--enable-l7-proxy)" subsys=daemon
```


恩ping通了，成功了!!!

> 甚至endpoin的都没变

待续。。。

<details>
<summary>云上的LOG</summary>

```sh

root@hcss-ecs-0616:~# sudo kubectl get pod -A -o wide
NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE     IP               NODE            NOMINATED NODE   READINESS GATES
default       busybox-4smlk                           1/1     Running   0          4s      10.0.0.95        hcss-ecs-0616   <none>           <none>
default       busybox-tzpbr                           1/1     Running   0          4s      10.0.1.138       k8s-109         <none>           <none>
kube-system   cilium-dshd8                            1/1     Running   0          8m43s   192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   cilium-kubeedge-ttrtg                   1/1     Running   0          3m8s    192.168.10.105   k8s-109         <none>           <none>
kube-system   cilium-operator-7c44c7db47-94lgh        1/1     Running   0          17m     192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   coredns-5bbd96d687-l46l6                1/1     Running   0          19m     10.0.0.146       hcss-ecs-0616   <none>           <none>
kube-system   coredns-5bbd96d687-pnwbd                1/1     Running   0          19m     10.0.0.143       hcss-ecs-0616   <none>           <none>
kube-system   etcd-hcss-ecs-0616                      1/1     Running   24         19m     192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   kube-apiserver-hcss-ecs-0616            1/1     Running   7          19m     192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   kube-controller-manager-hcss-ecs-0616   1/1     Running   5          19m     192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   kube-proxy-6kwrl                        1/1     Running   0          6m31s   192.168.10.105   k8s-109         <none>           <none>
kube-system   kube-proxy-sj26l                        1/1     Running   0          19m     192.168.10.106   hcss-ecs-0616   <none>           <none>
kube-system   kube-scheduler-hcss-ecs-0616            1/1     Running   26         19m     192.168.10.106   hcss-ecs-0616   <none>           <none>
kubeedge      cloudcore-5795465cb6-vnx2w              1/1     Running   0          7m21s   192.168.10.106   hcss-ecs-0616   <none>           <none>
kubeedge      edge-eclipse-mosquitto-5mdht            1/1     Running   0          6m30s   192.168.10.105   k8s-109         <none>           <none>

root@hcss-ecs-0616:~# sudo kubectl exec -it busybox-4smlk -- sh
/ # ping 10.0.1.138
PING 10.0.1.138 (10.0.1.138): 56 data bytes
64 bytes from 10.0.1.138: seq=3 ttl=60 time=9.384 ms
64 bytes from 10.0.1.138: seq=4 ttl=60 time=8.639 ms
64 bytes from 10.0.1.138: seq=5 ttl=60 time=8.706 ms

/# cilium-health status
Probe time:   2024-07-09T07:33:25Z
Nodes:
  kubernetes/hcss-ecs-0616 (localhost):
    Host connectivity to [192.168.10.106](http://192.168.10.106/):
      ICMP to stack:   OK, RTT=191.675µs
      HTTP to agent:   OK, RTT=162.035µs
    Endpoint connectivity to [10.0.0.114](http://10.0.0.114/):
      ICMP to stack:   OK, RTT=194.525µs
      HTTP to agent:   OK, RTT=253.218µs
  kubernetes/k8s-109:
    Host connectivity to [192.168.10.105](http://192.168.10.105/):
      ICMP to stack:   Connection timed out
      HTTP to agent:   Get "[http://192.168.10.105:4240/hello](http://192.168.10.105:4240/hello)": dial tcp [192.168.10.105:4240](http://192.168.10.105:4240/): connect: no route to host
    Endpoint connectivity to [10.0.1.84](http://10.0.1.84/):
      ICMP to stack:   Connection timed out
      HTTP to agent:   Get "[http://10.0.1.84:4240/hello](http://10.0.1.84:4240/hello)": dial tcp [10.0.1.84:4240](http://10.0.1.84:4240/): connect: no route to host

root@hcss-ecs-0616:~# sudo wg

interface: cilium_wg0
  public key: doCsdL7/fPO4qxFQk5gR2B2w8xdMB0zIGRTULBxBxz0=
  private key: (hidden)
  listening port: 51871
peer: k7+xehEQSDfzWZErB2tj6M7hNN3p6O2FaPDlXTEywko=
  endpoint: [192.168.10.105:51871](http://192.168.10.105:51871/)
  allowed ips: [10.0.1.84/32](http://10.0.1.84/32), [10.0.1.132/32](http://10.0.1.132/32), [10.0.1.138/32](http://10.0.1.138/32)
  latest handshake: 4 minutes ago
  transfer: 18.27 KiB received, 21.98 KiB sent

```
</details>


<details>
<summary>Local的LOG</summary>

```sh

k8s@k8s-109:~$ sudo wg
interface: cilium_wg0
  public key: k7+xehEQSDfzWZErB2tj6M7hNN3p6O2FaPDlXTEywko=
  private key: (hidden)
  listening port: 51871
peer: doCsdL7/fPO4qxFQk5gR2B2w8xdMB0zIGRTULBxBxz0=
  endpoint: [192.168.10.106:51871](http://192.168.10.106:51871/)
  allowed ips: [10.0.0.146/32](http://10.0.0.146/32), [10.0.0.143/32](http://10.0.0.143/32), [10.0.0.201/32](http://10.0.0.201/32), [10.0.0.114/32](http://10.0.0.114/32), [10.0.0.95/32](http://10.0.0.95/32)
  latest handshake: 1 minute, 47 seconds ago
  transfer: 18.07 KiB received, 18.57 KiB sent

k8s@k8s-109:~$ sudo crictl exec -it 2b76add85922f sh
/ # ping 10.0.0.95
PING 10.0.0.95 (10.0.0.95): 56 data bytes
64 bytes from [10.0.0.95](http://10.0.0.95/): seq=0 ttl=60 time=18.450 ms
64 bytes from [10.0.0.95](http://10.0.0.95/): seq=1 ttl=60 time=8.671 ms  

sudo crictl exec -it 9eeead76383e9 sh

/# cilium-health status
Probe time:   2024-07-09T07:34:24Z
Nodes:
  kubernetes/k8s-109 (localhost):
    Host connectivity to [192.168.10.105](http://192.168.10.105/):
      ICMP to stack:   OK, RTT=438.337µs
      HTTP to agent:   OK, RTT=490.203µs
    Endpoint connectivity to [10.0.1.84](http://10.0.1.84/):
      ICMP to stack:   OK, RTT=412.137µs
      HTTP to agent:   OK, RTT=565.312µs

  kubernetes/hcss-ecs-0616:
    Host connectivity to [192.168.10.106](http://192.168.10.106/):
      ICMP to stack:   OK, RTT=8.460022ms
      HTTP to agent:   OK, RTT=9.185251ms
    Endpoint connectivity to [10.0.0.114](http://10.0.0.114/):
      ICMP to stack:   Connection timed out
      HTTP to agent:   Get "[http://10.0.0.114:4240/hello](http://10.0.0.114:4240/hello)": dial tcp [10.0.0.114:4240](http://10.0.0.114:4240/): connect: no route to host  

```

</details>

