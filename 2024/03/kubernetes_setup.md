# K8s

- [Ubuntu Server 20.04 搭建安装 Kubernetes_Kubernetes\_玏佾\_InfoQ 写作社区](https://xie.infoq.cn/article/08ed85399b0d1fcc322056f2e)
- [Ubuntu 22.04 搭建 K8s 集群-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/2347138)
- [Installing kubernetes behind a corporate proxy | by Vivekanand Poojari | Medium](https://medium.com/@vivekanand.poojari/installing-kubernetes-behind-a-corporate-proxy-bc5582e43fb8)
- [Kubernetes 集群常用操作总结-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/1822355)

```sh
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of control-plane nodes by copying certificate authorities
and service account keys on each node and then running the following as root:

  kubeadm join 192.168.0.141:6443 --token abua9g.y6w2fs8617da979f \
	--discovery-token-ca-cert-hash sha256:2f0dfadb226a9c474402152278b7e7ce45e5173048db9aadf5113b0e4f454d80 \
	--control-plane

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.0.141:6443 --token abua9g.y6w2fs8617da979f \
	--discovery-token-ca-cert-hash sha256:2f0dfadb226a9c474402152278b7e7ce45e5173048db9aadf5113b0e4f454d80
```

kubeadm join 192.168.0.141:6443 --token fttqdm.9ohvqi7suyogle69 --discovery-token-ca-cert-hash e7d19167f02c297ae649ae3fd9e4fe6e8e131efe2c89d5a687bd99ab027d62ff --node-name=slave-1

## Prev

确保所有机器都安装了

### Docker

[Install Docker Engine on Ubuntu | Docker Docs](https://docs.docker.com/engine/install/ubuntu/)

docker proxy setting  
[Configure the daemon with systemd | Docker Docs](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)

```sh
sudo systemctl daemon-reload
sudo systemctl restart docker
sudo docker search ubuntu`
```

### CNI

[Release v1.4.1: Merge pull request #991 from containernetworking/dependabot/docker/do… · containernetworking/plugins · GitHub](https://github.com/containernetworking/plugins/releases/tag/v1.4.1)

```
sudo tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.4.1.tgz
```

### Kubeadmin

[Installing kubeadm | Kubernetes](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

## master

master's ip: 192.168.0.141

```sh
swapoff -a
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --control-plane-endpoint=192.168.0.141
```

[error execution phase addon/coredns: unable to create a new DNS service: rpc error: code = Unknown desc = malformed header: missing HTTP content-type · Issue #2792 · kubernetes/kubeadm · GitHub](https://github.com/kubernetes/kubeadm/issues/2792)

### forbidden

[Installing Kubernetes with the Flannel Network Plugin on CentOS 7 · GitHub](https://gist.github.com/rkaramandi/44c7cea91501e735ea99e356e9ae7883)

```
      [plugins."io.containerd.grpc.v1.cri".containerd.runtimes]
        [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
        ...
          [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
            SystemdCgroup = true
```

#### 部署网络插件

##### flannel

```sh
$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

Warning: policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
podsecuritypolicy.policy/psp.flannel.unprivileged created
clusterrole.rbac.authorization.k8s.io/flannel created
clusterrolebinding.rbac.authorization.k8s.io/flannel created
serviceaccount/flannel created
configmap/kube-flannel-cfg created
daemonset.apps/kube-flannel-ds created
```

### 查看 master 是不成功启动

kubectl get pods --all-namespaces

kubectl describe pod coredns-76f75df574-sjb9n -n kube-system

### 确保集群内部没有设置代理

默认初始化时是继承 host 的环境  
sudo vim /etc/kubernetes/manifests/kube-apiserver.yaml

```sh
env:
 - name: http_proxy
   value: http://xxx:port
```

### coredns 启动失败 failed to find plugin “xxx“ in path [/opt/cni/bin]]

确保已经安装 cni

## Slave

### slave kubeadm join error

```sh
ultraman@ultraman0x01:~$ sudo kubeadm join 192.168.0.141:6443 --token fbpwhp.uvoucr6tysty28yy --discovery-token-ca-cert-hash sha256:84676b239154c20c1e252a7635e7105617fc4fdaeb70e6c489bd95d0b6dad96a --node-name=node1
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR CRI]: container runtime is not running: output: time="2024-03-28T13:24:30+08:00" level=fatal msg="validate service connection: validate CRI v1 runtime API for endpoint \"unix:///var/run/containerd/containerd.sock\": rpc error: code = Unimplemented desc = unknown service runtime.v1.RuntimeService"
, error: exit status 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher
```

[\[ERROR CRI\]: container runtime is not running: output · Issue #8139 · containerd/containerd · GitHub](https://github.com/containerd/containerd/issues/8139)  
sudo rm /etc/containerd/config.toml  
systemctl restart containerd

## 部署个 Hello world

选确认下 k8s 的状态是否都正常

```sh
15:26:41 ultraman@master ~ kubectl get nodes -o wide
NAME     STATUS   ROLES           AGE   VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
master   Ready    control-plane   39m   v1.29.3   192.168.0.141   <none>        Ubuntu 22.04.4 LTS   6.5.0-27-generic   containerd://2.0.0-rc.0
node1    Ready    <none>          34m   v1.29.3   192.168.0.142   <none>        Ubuntu 22.04.4 LTS   6.5.0-26-generic   containerd://1.6.28
15:32:54 ultraman@master ~ kubectl get pods --all-namespaces
NAMESPACE      NAME                             READY   STATUS             RESTARTS        AGE
default        hello-world-89cc8fdcd-6cvrj      1/1     Running            5 (5m16s ago)   28m
kube-flannel   kube-flannel-ds-gnfpf            1/1     Running            0               37m
kube-flannel   kube-flannel-ds-n8pq7            0/1     CrashLoopBackOff   9 (20s ago)     34m
kube-system    coredns-76f75df574-jkbrq         1/1     Running            0               38m
kube-system    coredns-76f75df574-xwkmr         1/1     Running            0               38m
kube-system    etcd-master                      1/1     Running            20              39m
kube-system    kube-apiserver-master            1/1     Running            0               39m
kube-system    kube-controller-manager-master   1/1     Running            0               39m
kube-system    kube-proxy-27v58                 0/1     CrashLoopBackOff   8 (3m30s ago)   34m
kube-system    kube-proxy-fbql5                 1/1     Running            0               38m
kube-system    kube-scheduler-master            1/1     Running            0               39m
```

```sh
kubectl create development hello-world --image=nginx
kubectl expose deployment hello-world --type=NodePort --port=80
```

```sh
15:34:49 ultraman@master ~ kubectl get deployment                                                                                                                  1 ↵
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
hello-world   0/1     1            0           30m
15:34:52 ultraman@master ~ kubectl get services
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
hello-world   NodePort    10.104.186.199   <none>        80:32719/TCP   30m
kubernetes    ClusterIP   10.96.0.1        <none>        443/TCP        41m
15:35:11 ultraman@master ~ kubectl get svc hello-world                                                                                                             1 ↵
NAME          TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
hello-world   NodePort   10.104.186.199   <none>        80:32719/TCP   30m
```

使用 internal_ip:port 访问 http://192.168.0.142:32719/

## Helm

[Helm](https://helm.sh/)
