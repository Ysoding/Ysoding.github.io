## 场景问题

cilium CNI 安装在 kubeedge 边缘节点上的 pod 每隔一段时间就会崩溃。

```sh
kubectl get pods -o wide -A --watch
NAMESPACE     NAME                                      READY   STATUS    RESTARTS        AGE     IP              NODE              NOMINATED NODE   READINESS GATES
kube-system   cilium-edgecore-bt9w4                     1/1     Running   2 (106s ago)    2m42s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-k6dll                              1/1     Running   0               3h47m   192.168.0.141   master-ultraman   <none>           <none>
kube-system   cilium-operator-699f6d4568-v9zrr          1/1     Running   3 (3h11m ago)   3h48m   192.168.0.141   master-ultraman   <none>           <none>
kube-system   coredns-5dd5756b68-6d6s6                  1/1     Running   0               3h52m   10.0.0.204      master-ultraman   <none>           <none>
kube-system   coredns-5dd5756b68-fx52s                  1/1     Running   0               3h52m   10.0.0.250      master-ultraman   <none>           <none>
kube-system   etcd-master-ultraman                      1/1     Running   0               3h52m   192.168.0.141   master-ultraman   <none>           <none>
kube-system   kube-apiserver-master-ultraman            1/1     Running   2 (3h6m ago)    3h52m   192.168.0.141   master-ultraman   <none>           <none>
kube-system   kube-controller-manager-master-ultraman   1/1     Running   2 (3h11m ago)   3h52m   192.168.0.141   master-ultraman   <none>           <none>
kube-system   kube-proxy-psjzf                          1/1     Running   1 (8s ago)      2m13s   192.168.0.142   edgezz1           <none>           <none>
kube-system   kube-proxy-rxb7z                          1/1     Running   0               3h52m   192.168.0.141   master-ultraman   <none>           <none>
kube-system   kube-scheduler-master-ultraman            1/1     Running   2 (3h11m ago)   3h52m   192.168.0.141   master-ultraman   <none>           <none>
kubeedge      cloudcore-66595d548c-642dv                1/1     Running   0               3h43m   192.168.0.141   master-ultraman   <none>           <none>
kubeedge      edge-eclipse-mosquitto-bm7ss              1/1     Running   0               52s     192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Completed   2 (2m19s ago)   3m15s   192.168.0.142   edgezz1           <none>           <none>
kubeedge      edge-eclipse-mosquitto-bm7ss              0/1     Completed   0               85s     192.168.0.142   edgezz1           <none>           <none>
kubeedge      edge-eclipse-mosquitto-bm7ss              1/1     Running     1 (<invalid> ago)   86s     192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Completed   2                   3m16s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Completed   2                   3m17s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Completed   2                   3m18s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Completed   2                   3m19s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Completed   2                   3m20s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Completed   2                   3m21s   192.168.0.142   edgezz1           <none>           <none>
kube-system   kube-proxy-psjzf                          0/1     Error       1 (47s ago)         2m52s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     CrashLoopBackOff   2 (<invalid> ago)   3m22s   192.168.0.142   edgezz1           <none>           <none>
kube-system   kube-proxy-psjzf                          0/1     CrashLoopBackOff   1 (<invalid> ago)   2m53s   192.168.0.142   edgezz1           <none>           <none>
kube-system   kube-proxy-psjzf                          1/1     Running            2 (<invalid> ago)   3m6s    192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Running            3                   3m44s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Running            3                   3m49s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     0/1     Running            3                   3m49s   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-edgecore-bt9w4                     1/1     Running            3                   3m49s   192.168.0.142   edgezz1           <none>           <none>
```

## 排查

首先查看几个关键 pod 的日志包括 containerd，并不认为有任何有用的信息。

后面找到一个相关的解决方案  
[# kube-flannel pod 不断重启](https://www.sundayhk.com/post/kube-flannel-always-restarting/)

确实能解决，然后呢？设置这有什么用？本质是什么？ （装逼三连问 bushi）

resources:

- [Configuring a cgroup driver](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/configure-cgroup-driver/)
- [CRI Plugin Config Guide](https://github.com/containerd/containerd/blob/main/docs/cri/config.md)

经过研究、

我发现官方文件中有一点提到 [Link](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd)

```
If you installed containerd from a package (for example, RPM or `.deb`), you may find that the CRI integration plugin is disabled by default.

You need CRI support enabled to use containerd with Kubernetes. Make sure that `cri` is not included in the`disabled_plugins` list within `/etc/containerd/config.toml`; if you made changes to that file, also restart `containerd`.

If you experience container crash loops after the initial cluster installation or after installing a CNI, the containerd configuration provided with the package might contain incompatible configuration parameters. Consider resetting the containerd configuration with `containerd config default > /etc/containerd/config.toml` as specified in [getting-started.md](https://github.com/containerd/containerd/blob/main/docs/getting-started.md#advanced-topics) and then set the configuration parameters specified above accordingly.
```

在检查 containerd 版本时，我发现边缘节点上的版本不是 1.7.2，明明我之前手动安装的版本是 1.7.2。

```sh
{24-04-18 11:30}master:~/test ultraman% kubectl get nodes -o wide -A
NAME              STATUS   ROLES           AGE    VERSION                    INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
edgezz1           Ready    agent,edge      158m   v1.27.7-kubeedge-v1.16.1   192.168.0.142   <none>        Ubuntu 22.04.4 LTS   6.5.0-26-generic   containerd://1.6.31
master-ultraman   Ready    control-plane   168m   v1.28.9                    192.168.0.141   <none>        Ubuntu 22.04.4 LTS   6.5.0-27-generic   containerd://1.7.2
```

```
ultraman@node1:~$ containerd --version
containerd github.com/containerd/containerd v1.7.2 0cae528dd6cb557f7201036e9f43420650207b58
```

原来，在安装 docker 时已经安装了 containerd，与我手动安装的位置并不匹配。

```sh
ultraman@node1:~/test$ sudo systemctl status containerd
● containerd.service - containerd container runtime
     Loaded: loaded (/lib/systemd/system/containerd.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/containerd.service.d
             └─http-proxy.conf
     Active: active (running) since Thu 2024-04-18 13:07:28 CST; 3min 26s ago
       Docs: https://containerd.io
    Process: 1147466 ExecStartPre=/sbin/modprobe overlay (code=exited, status=0/SUCCESS)
   Main PID: 1147467 (containerd)
      Tasks: 71
     Memory: 980.8M
        CPU: 24.645s
     CGroup: /system.slice/containerd.service
```

编辑 containerd.service

`ExecStart=/usr/bin/containerd -> ExecStart=/usr/local/bin/containerd`

`sudo systemctl restart containerd`

```sh
kubectl get nodes -o wide -A
NAME              STATUS   ROLES           AGE     VERSION                    INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
edgezz1           Ready    agent,edge      4h12m   v1.27.7-kubeedge-v1.16.1   192.168.0.142   <none>        Ubuntu 22.04.4 LTS   6.5.0-27-generic   containerd://1.7.2
master-ultraman   Ready    control-plane   4h23m   v1.28.9                    192.168.0.141   <none>        Ubuntu 22.04.4 LTS   6.5.0-27-generic   containerd://1.7.2
```

最后发现，问题并不是出在这个地方。

后面我支查看了 Ubuntu 22 和 Ubuntu 20 在默认 cgroup 支持方面的差异 [linux-distribution-cgroup-v2-support](https://kubernetes.io/docs/concepts/architecture/cgroups/#linux-distribution-cgroup-v2-support).

For cgroup v2, the output is `cgroup2fs`.

For cgroup v1, the output is `tmpfs.`

```sh
k8s@edge121:~$ stat -fc %T /sys/fs/cgroup/
tmpfs
k8s@edge121:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.6 LTS
Release:	20.04
Codename:	focal
```

```
ultraman@node1:~$ stat -fc %T /sys/fs/cgroup/
cgroup2fs
ultraman@node1:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.4 LTS
Release:	22.04
Codename:	jammy
```

然后，我强迫 ubuntu22 改为 cgroup v1

```sh
ultraman@node1:~$ sudo vim /etc/default/grub
ultraman@node1:~$ cat /etc/default/grub
# If you change this file, run 'update-grub' afterwards to update
# /boot/grub/grub.cfg.
# For full documentation of the options in this file, see:
#   info -f grub -n 'Simple configuration'

GRUB_DEFAULT=0
GRUB_TIMEOUT_STYLE=hidden
GRUB_TIMEOUT=0
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX="systemd.unified_cgroup_hierarchy=0"
```

添加 `systemd.unified_cgroup_hierarchy=0` 到 `GRUB_CMDLINE_LINUX`.

更新重启

```sh
sudo update-grub
sudo reboot
```

检测版本

```sh
ultraman@node1:~$ stat -fc %T /sys/fs/cgroup/
tmpfs
```

问题又解决了，无需设置 SystemdCgroup

```sh
ultraman@master ~/test$ kubectl get pods -o wide -A
NAMESPACE     NAME                                      READY   STATUS    RESTARTS      AGE   IP              NODE              NOMINATED NODE   READINESS GATES
kube-system   cilium-edgecore-rdc95                     1/1     Running   0             15m   192.168.0.142   edgezz1           <none>           <none>
kube-system   cilium-k6dll                              1/1     Running   0             23h   192.168.0.141   master-ultraman   <none>           <none>
kube-system   cilium-operator-699f6d4568-v9zrr          1/1     Running   4 (17h ago)   23h   192.168.0.141   master-ultraman   <none>           <none>
kube-system   coredns-5dd5756b68-6d6s6                  1/1     Running   0             24h   10.0.0.204      master-ultraman   <none>           <none>
kube-system   coredns-5dd5756b68-fx52s                  1/1     Running   0             24h   10.0.0.250      master-ultraman   <none>           <none>
kube-system   etcd-master-ultraman                      1/1     Running   0             24h   192.168.0.141   master-ultraman   <none>           <none>
kube-system   kube-apiserver-master-ultraman            1/1     Running   2 (23h ago)   24h   192.168.0.141   master-ultraman   <none>           <none>
kube-system   kube-controller-manager-master-ultraman   1/1     Running   2 (23h ago)   24h   192.168.0.141   master-ultraman   <none>           <none>
kube-system   kube-proxy-cq4cb                          1/1     Running   0             19m   192.168.0.142   edgezz1           <none>           <none>
kube-system   kube-proxy-rxb7z                          1/1     Running   0             24h   192.168.0.141   master-ultraman   <none>           <none>
kube-system   kube-scheduler-master-ultraman            1/1     Running   3 (17h ago)   24h   192.168.0.141   master-ultraman   <none>           <none>
kubeedge      cloudcore-66595d548c-642dv                1/1     Running   0             23h   192.168.0.141   master-ultraman   <none>           <none>
kubeedge      edge-eclipse-mosquitto-bqtp2              1/1     Running   0             14m   192.168.0.142   edgezz1           <none>           <none>
```

## 结论

因此我的结论是，在 Ubuntu 22 中，默认使用 cgroup v2，而 Ubuntu 20 则使用 cgroup v1。因此，当我们使用 "sudo keadm join --cloudcore-ipport=192.168.0.141:10000 --kubeedge-version=v1.16.1 --cgroupdriver=systemd "加入指定的 systemd cgroup 驱动程序时，在启用了 cgroup v2 的系统中，我们需要在 Containerd 中启用 SystemdCgroup，如官方文档中所述 [Configuring the systemd cgroup driver](Configuring the systemd cgroup driver)

```
To use the systemd cgroup driver in /etc/containerd/config.toml with runc, set

[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  …
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
The systemd cgroup driver is recommended if you use cgroup v2.
```
