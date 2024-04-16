## 问题

在 K8s 中使用 Cilium CNI 时，`cilium connectivity test`会发现有些连接 1.1.1.1/1.0.0.1 的出错日志，大概率是因为地址被禁了。

如何通过呢？

## 尝试一(fail)： 向 cilium-test namespace 下所有的 pod 设置 proxy。

```sh
root@k8s-134:/home/k8s# cat cilium_test_envset.sh
#!/bin/bash
kubectl set env deployment/echo-same-node -n cilium-test no_proxy=10.0.0.0/8,192.168.0.0/24,127.0.0.1,echo-same-node.cilium-test
kubectl set env deployment/client -n cilium-test no_proxy=10.0.0.0/8,192.168.0.0/24,127.0.0.1,echo-same-node.cilium-test
kubectl set env deployment/client2 -n cilium-test no_proxy=10.0.0.0/8,192.168.0.0/24,127.0.0.1,echo-same-node.cilium-test
kubectl set env deployment/client -n cilium-test http_proxy=http://host:ip/
kubectl set env deployment/client2 -n cilium-test http_proxy=http://host:ip/
kubectl set env deployment/echo-same-node -n cilium-test http_proxy=http://host:ip/
kubectl set env deployment/client -n cilium-test https_proxy=http://host:ip/
kubectl set env deployment/client2 -n cilium-test https_proxy=http://host:ip/
kubectl set env deployment/echo-same-node -n cilium-test https_proxy=http://host:ip/
```

访问都没问题了，现在出错不是网络访问不了外网的的问题，仔细研究几个出错的测试。

有个测试是由于`curl echo-same-node.cilium-test`时走了 proxy 导致访问出错，已经在上面设置了 no_proxy。

发现某个测试在 apply 相应的 network policy 中限制了 proxy 的地址，导致不能访问代理。

为了过这个测试，修改测试对应的安全规则，手动编译运行。

```sh
--- a/connectivity/builder/manifests/client-egress-to-cidr-external-knp.yaml
+++ b/connectivity/builder/manifests/client-egress-to-cidr-external-knp.yaml
@@ -13,3 +13,5 @@ spec:
             cidr: {{.ExternalCIDR}}
             except:
               - {{.ExternalOtherIP}}/32
+        - ipBlock:
+            cidr: xxx.0.0.0/8   --> add the proxy IP address
diff --git a/connectivity/builder/manifests/client-egress-to-cidr-external.yaml b/connectivity/builder/manifests/client-egress-to-cidr-external.yaml
index b2fa10ca..c3e4291a 100644
--- a/connectivity/builder/manifests/client-egress-to-cidr-external.yaml
+++ b/connectivity/builder/manifests/client-egress-to-cidr-external.yaml
@@ -9,6 +9,7 @@ spec:
       kind: client
   egress:
   - toCIDRSet:
+    - cidr: xxx.0.0.0/8           --> add the proxy IP address
     - cidr: {{.ExternalCIDR}}
       except:
       - {{.ExternalOtherIP}}/32
```

但是仍然有未通过的，原因与上一样，但是都不能修改对应的规则。

因为规则类似这种，禁止 1.0.0.0/8，除了 1.1.1.1/32 访问，此时应该禁止访问 1.0.0.1/32 但是 curl 会走 proxy 导致通过 proxy 访问是可以的。应该是无解吧？

## 尝试二(ok)：自建两个 https 服务，改变访问的 external 地址。

这里使用 Caddy 来搭建 https web server（别问为什么，因为搭建 https 太快了，Caddy 牛逼）

创建个 Caddyfile

```Caddyfile
:443, localhost, 192.168.0.142 {
  respond "Hello, world!"
}
```

直接 caddy reload

所有测试通过

```
cilium connectivity test --test '!pod-to-world' --external-cidr 192.168.0.0/24 --external-ip 192.168.0.142 --external-other-ip 192.168.0.141 --request-timeout 1s --connect-timeout 2s --curl-insecure true
✅ All 43 tests (170 actions) successful, 32 tests skipped, 5 scenarios skipped.
```
