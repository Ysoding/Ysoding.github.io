
## 表象

执行cilium connectivity test时出现大量unexpected EOF

```sh
vagrant@cloud01:~/scripts$ sudo cilium connectivity test --test '!pod-to-cidr,!pod-to-world,!health' --verbose
ℹ️  Single-node environment detected, enabling single-node connectivity test
ℹ️  Monitor aggregation detected, will skip some flow validation steps
✨ [kubernetes] Creating namespace cilium-test for connectivity check...
✨ [kubernetes] Deploying echo-same-node service...
✨ [kubernetes] Deploying DNS test server configmap...
✨ [kubernetes] Deploying same-node deployment...
✨ [kubernetes] Deploying client deployment...
✨ [kubernetes] Deploying client2 deployment...
⌛ [kubernetes] Waiting for deployment cilium-test/client to become ready...
⌛ [kubernetes] Waiting for deployment cilium-test/client2 to become ready...
⌛ [kubernetes] Waiting for deployment cilium-test/echo-same-node to become ready...
⌛ [kubernetes] Waiting for pod cilium-test/client-56f8968958-l5n2q to reach DNS server on cilium-test/echo-same-node-6b8d95757c-sbrht pod...
⌛ [kubernetes] Waiting for pod cilium-test/client2-5668f9f59b-c6fml to reach DNS server on cilium-test/echo-same-node-6b8d95757c-sbrht pod...
⌛ [kubernetes] Waiting for pod cilium-test/client-56f8968958-l5n2q to reach default/kubernetes service...
⌛ [kubernetes] Waiting for pod cilium-test/client2-5668f9f59b-c6fml to reach default/kubernetes service...
⌛ [kubernetes] Waiting for Service cilium-test/echo-same-node to become ready...
⌛ [kubernetes] Waiting for Service cilium-test/echo-same-node to be synchronized by Cilium pod kube-system/cilium-kubeedge-f6qrc
⌛ [kubernetes] Waiting for NodePort 192.168.56.20:32371 (cilium-test/echo-same-node) to become ready...
⌛ [kubernetes] Waiting for NodePort 192.168.56.24:32371 (cilium-test/echo-same-node) to become ready...
⌛ [kubernetes] Waiting for NodePort 192.168.56.23:32371 (cilium-test/echo-same-node) to become ready...
ℹ️  Skipping IPCache check
🔭 Enabling Hubble telescope...
⚠️  Unable to contact Hubble Relay, disabling Hubble telescope and flow validation: rpc error: code = Unavailable desc = connection error: desc = "transport: Error while dialing: dial tcp 127.0.0.1:4245: connect: connection refused"
ℹ️  Expose Relay locally with:
cilium hubble enable
cilium hubble port-forward&
ℹ️  Cilium version: 1.15.3
🏃 Running 75 tests ...
[=] Test [echo-ingress-l7] [50/75]
ℹ️  Cilium agent kube-system/cilium-kubeedge-f6qrc logs since 2024-05-24 01:20:08.657972058 +0000 UTC m=+34.021521054:
ℹ️  Cilium agent kube-system/cilium-kubeedge-njvjk logs since 2024-05-24 01:20:08.657972058 +0000 UTC m=+34.021521054:
ℹ️  Cilium agent kube-system/cilium-dd4gt logs since 2024-05-24 01:20:08.657972058 +0000 UTC m=+34.021521054:
🟥 Running test echo-ingress-l7: setting up test: applying network policies: unable to get policy revisions for Cilium pods: error with exec request (pod=kube-system/cilium-kubeedge-f6qrc, container=cilium-agent): unexpected EOF
[=] Test [echo-ingress-l7-named-port] [51/75]
ℹ️  Cilium agent kube-system/cilium-kubeedge-f6qrc logs since 2024-05-24 01:20:09.256051924 +0000 UTC m=+34.619600921:
ℹ️  Cilium agent kube-system/cilium-kubeedge-njvjk logs since 2024-05-24 01:20:09.256051924 +0000 UTC m=+34.619600921:
ℹ️  Cilium agent kube-system/cilium-dd4gt logs since 2024-05-24 01:20:09.256051924 +0000 UTC m=+34.619600921:
🟥 Running test echo-ingress-l7-named-port: setting up test: applying network policies: unable to get policy revisions for Cilium pods: error with exec request (pod=kube-system/cilium-kubeedge-f6qrc, container=cilium-agent): unexpected EOF
[=] Test [client-egress-l7-method] [52/75]
ℹ️  Cilium agent kube-system/cilium-kubeedge-f6qrc logs since 2024-05-24 01:20:09.856755252 +0000 UTC m=+35.220304248:
ℹ️  Cilium agent kube-system/cilium-kubeedge-njvjk logs since 2024-05-24 01:20:09.856755252 +0000 UTC m=+35.220304248:
ℹ️  Cilium agent kube-system/cilium-dd4gt logs since 2024-05-24 01:20:09.856755252 +0000 UTC m=+35.220304248:
🟥 Running test client-egress-l7-method: setting up test: applying network policies: unable to get policy revisions for Cilium pods: error with exec request (pod=kube-system/cilium-kubeedge-f6qrc, container=cilium-agent): unexpected EOF
[=] Test [client-egress-l7] [53/75]
ℹ️  Cilium agent kube-system/cilium-dd4gt logs since 2024-05-24 01:20:10.457217689 +0000 UTC m=+35.820766693:
ℹ️  Cilium agent kube-system/cilium-kubeedge-f6qrc logs since 2024-05-24 01:20:10.457217689 +0000 UTC m=+35.820766693:
ℹ️  Cilium agent kube-system/cilium-kubeedge-njvjk logs since 2024-05-24 01:20:10.457217689 +0000 UTC m=+35.820766693:
🟥 Running test client-egress-l7: setting up test: applying network policies: unable to get policy revisions for Cilium pods: error with exec request (pod=kube-system/cilium-kubeedge-f6qrc, container=cilium-agent): unexpected EOF
[=] Test [client-egress-l7-named-port] [54/75]
ℹ️  Cilium agent kube-system/cilium-dd4gt logs since 2024-05-24 01:20:11.059164266 +0000 UTC m=+36.422713266:
ℹ️  Cilium agent kube-system/cilium-kubeedge-f6qrc logs since 2024-05-24 01:20:11.059164266 +0000 UTC m=+36.422713266:
ℹ️  Cilium agent kube-system/cilium-kubeedge-njvjk logs since 2024-05-24 01:20:11.059164266 +0000 UTC m=+36.422713266:
🟥 Running test client-egress-l7-named-port: setting up test: applying network policies: unable to get policy revisions for Cilium pods: error with exec request (pod=kube-system/cilium-kubeedge-f6qrc, container=cilium-agent): unexpected EOF
[=] Test [dns-only] [69/75]
ℹ️  Cilium agent kube-system/cilium-dd4gt logs since 2024-05-24 01:20:11.660165979 +0000 UTC m=+37.023714982:
ℹ️  Cilium agent kube-system/cilium-kubeedge-f6qrc logs since 2024-05-24 01:20:11.660165979 +0000 UTC m=+37.023714982:
ℹ️  Cilium agent kube-system/cilium-kubeedge-njvjk logs since 2024-05-24 01:20:11.660165979 +0000 UTC m=+37.023714982:
🟥 Running test dns-only: setting up test: applying network policies: unable to get policy revisions for Cilium pods: error with exec request (pod=kube-system/cilium-kubeedge-f6qrc, container=cilium-agent): unexpected EOF
📋 Test Report
❌ 29/38 tests failed (0/63 actions), 37 tests skipped, 4 scenarios skipped:
Test [all-ingress-deny-knp]:
Test [all-egress-deny]:
Test [all-egress-deny-knp]:
Test [all-entities-deny]:
Test [cluster-entity]:
Test [host-entity-egress]:
Test [host-entity-ingress]:
Test [echo-ingress]:
Test [echo-ingress-knp]:
Test [client-ingress-icmp]:
Test [client-egress]:
Test [client-egress-knp]:
Test [client-egress-expression]:
Test [client-egress-expression-knp]:
Test [client-with-service-account-egress-to-echo]:
Test [client-egress-to-echo-service-account]:
Test [echo-ingress-from-other-client-deny]:
Test [client-ingress-from-other-client-icmp-deny]:
Test [client-egress-to-echo-deny]:
Test [client-ingress-to-echo-named-port-deny]:
Test [client-egress-to-echo-expression-deny]:
Test [client-with-service-account-egress-to-echo-deny]:
Test [client-egress-to-echo-service-account-deny]:
Test [echo-ingress-l7]:
Test [echo-ingress-l7-named-port]:
Test [client-egress-l7-method]:
Test [client-egress-l7]:
Test [client-egress-l7-named-port]:
Test [dns-only]:
connectivity test failed: 29 tests failed
```




## 排查

首先去看了代码，找出可能的断开原因，主要有timeout, 一个http请求server端意外断开都会产生`unexpected eof`但是timeout日志会输出是因为timeout。


那么Server端为什么会意外关闭呢？

有一个主要表现就是pod被重启了，折磨了好久没有思路。


最后发现Vagrantfile 配置节点的默认内存为 2GB，在执行 cilium connectivity test时，会有一些 pod 部署到节点上，内存不足会导致运行过程中重新启动 pod（unexpected EOF）。

因此，升级到 4GB 内存时，一切正常。

```sh
nodes = ENV["INFRA_DUMMY_EDGE_NODES"].split(" ")
for node in nodes do
  ip_addr=ENV["INFRA_NODE_IP_#{node}"]
  servers.push({
    :name => node,
    :ip => ip_addr,
    :memory => 2048,
    :cpus => 2,
  })
end

```
