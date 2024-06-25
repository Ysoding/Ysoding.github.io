
#wireguard

### ENV

| name    | type   | machine   | IP                   |
| ------- | ------ | --------  | -------------------- |
| aws     | master | physical  | 52.52.52.1         |
| slave01 | slave  | physical  | 192.168.0.111        |
| slave02 | slave  | physical  | 43.22.33.11         |

`Notice:  slave02 CAN NOT ping slave01`

### Configure
1. 生成key

    ```
    $ wg genkey > xxx.key
    $ wg pubkey < xxx.key > xxx.pub
    ```


    ```
    # slave01
    # cat endpoint-a.key
    KNmFYtY70K+KJuh1fmAwyiFhBzGfk336fNttmPrWl2Y=
    # cat endpoint-a.pub
    sWYDVXZls20w83WIfj+fjyCgpsF2nwrg+esB3YxncEo=
    
    # slave02
    # cat endpoint-b.key
    oGz/xJ39uXX8MUIm9ZIKlnnES0MhuRmE93Gij7jCEms=
    # cat endpoint-b.pub
    WDR9qgEJvUzGv08lFpls1dHf6Av9IV1N81SF1BEHxmk=
    
    # master
    # cat host-c.key
    GBgag8YL7XaSCxdpULiN5F1cUKmpN5Wf2qD+ttPhWWk=
    # cat host-c.pub
    IZIVrlvAmBoc9BN4ahA/kxzxp4Z5nQwLSZdhlKiBOSo=
    ```

2. Wireguard 配置
    
    - slave01

        ```
        # cat /etc/wireguard/wg0.conf
        # /etc/wireguard/wg0.conf
        
        # local settings for Endpoint A
        [Interface]
        PrivateKey = KNmFYtY70K+KJuh1fmAwyiFhBzGfk336fNttmPrWl2Y=
        Address = 10.0.0.1/32
        ListenPort = 51821
        
        # remote settings for Host C
        [Peer]
        PublicKey = IZIVrlvAmBoc9BN4ahA/kxzxp4Z5nQwLSZdhlKiBOSo=
        Endpoint = 52.52.52.1:51823
        AllowedIPs = 10.0.0.0/24
        PersistentKeepalive = 25
        ```

    - slave02

        ```
        # cat /etc/wireguard/wg0.conf
        # /etc/wireguard/wg0.conf
        
        # local settings for Endpoint B
        [Interface]
        PrivateKey = oGz/xJ39uXX8MUIm9ZIKlnnES0MhuRmE93Gij7jCEms=
        Address = 10.0.0.2/32
        ListenPort = 51822
        
        # remote settings for Host C
        [Peer]
        PublicKey = IZIVrlvAmBoc9BN4ahA/kxzxp4Z5nQwLSZdhlKiBOSo=
        Endpoint = 52.52.52.1:51823
        AllowedIPs = 10.0.0.0/24
        PersistentKeepalive = 25
        ```

    - master

        ```
        # cat /etc/wireguard/wg0.conf
        # local settings for Host C
        [Interface]
        PrivateKey = GBgag8YL7XaSCxdpULiN5F1cUKmpN5Wf2qD+ttPhWWk=
        Address = 10.0.0.3/32
        ListenPort = 51823
        
        PreUp = sysctl -w net.ipv4.ip_forward=1
        
        # remote settings for Endpoint A
        [Peer]
        PublicKey = sWYDVXZls20w83WIfj+fjyCgpsF2nwrg+esB3YxncEo=
        AllowedIPs = 10.0.0.1/32
        
        # remote settings for Endpoint B
        [Peer]
        PublicKey = WDR9qgEJvUzGv08lFpls1dHf6Av9IV1N81SF1BEHxmk=
        AllowedIPs = 10.0.0.2/32
        ```
3. 启动wireguard
    ```
    $ sudo systemctl enable wg-quick@wg0.service
    $ sudo systemctl start wg-quick@wg0.service
    ```

### Test

1. Ping test
    
    - master --> slave01

        ```
        root@ip-172-31-33-234:/home/ubuntu# ping -c3 10.0.0.1
        PING 10.0.0.1 (10.0.0.1) 56(84) bytes of data.
        64 bytes from 10.0.0.1: icmp_seq=1 ttl=64 time=94.8 ms
        64 bytes from 10.0.0.1: icmp_seq=2 ttl=64 time=95.3 ms
        64 bytes from 10.0.0.1: icmp_seq=3 ttl=64 time=95.1 ms
        
        --- 10.0.0.1 ping statistics ---
        3 packets transmitted, 3 received, 0% packet loss, time 2001ms
        rtt min/avg/max/mdev = 94.812/95.077/95.283/0.196 ms
        ```

    - slave01 --> master

        ```
        root@adsl:/home/cscdev# ping -c3 10.0.0.3
        PING 10.0.0.3 (10.0.0.3) 56(84) bytes of data.
        64 bytes from 10.0.0.3: icmp_seq=1 ttl=64 time=94.5 ms
        64 bytes from 10.0.0.3: icmp_seq=2 ttl=64 time=95.4 ms
        64 bytes from 10.0.0.3: icmp_seq=3 ttl=64 time=99.6 ms
        
        --- 10.0.0.3 ping statistics ---
        3 packets transmitted, 3 received, 0% packet loss, time 2003ms
        rtt min/avg/max/mdev = 94.472/96.485/99.631/2.253 ms
        root@adsl:/home/cscdev#
        ```

    
    - master --> slave02

        ```
        root@ip-172-31-33-234:/home/ubuntu# ping -c3 10.0.0.2
        PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
        64 bytes from 10.0.0.2: icmp_seq=1 ttl=64 time=93.1 ms
        64 bytes from 10.0.0.2: icmp_seq=2 ttl=64 time=94.5 ms
        64 bytes from 10.0.0.2: icmp_seq=3 ttl=64 time=93.2 ms
        
        --- 10.0.0.2 ping statistics ---
        3 packets transmitted, 3 received, 0% packet loss, time 2003ms
        rtt min/avg/max/mdev = 93.122/93.610/94.530/0.650 ms
        ```

    - slave02 --> master

        ```
        root@cloud01:/home/cscdev# ping -c3 10.0.0.3
        PING 10.0.0.3 (10.0.0.3) 56(84) bytes of data.
        64 bytes from 10.0.0.3: icmp_seq=1 ttl=64 time=95.2 ms
        64 bytes from 10.0.0.3: icmp_seq=2 ttl=64 time=94.1 ms
        64 bytes from 10.0.0.3: icmp_seq=3 ttl=64 time=98.3 ms
        
        --- 10.0.0.3 ping statistics ---
        3 packets transmitted, 3 received, 0% packet loss, time 2002ms
        rtt min/avg/max/mdev = 94.068/95.865/98.317/1.795 ms
        root@cloud01:/home/cscdev#
        ```
    
    - slave01 --> slave02

        ```
        root@adsl:/home/cscdev# ping -c3 10.0.0.2
        PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
        64 bytes from 10.0.0.2: icmp_seq=1 ttl=63 time=188 ms
        64 bytes from 10.0.0.2: icmp_seq=2 ttl=63 time=188 ms
        64 bytes from 10.0.0.2: icmp_seq=3 ttl=63 time=188 ms
        
        --- 10.0.0.2 ping statistics ---
        3 packets transmitted, 3 received, 0% packet loss, time 2003ms
        rtt min/avg/max/mdev = 187.910/188.046/188.221/0.129 ms
        ```

    - slave02 --> slave01

        ```
        root@cloud01:/home/cscdev# ping -c3 10.0.0.1
        PING 10.0.0.1 (10.0.0.1) 56(84) bytes of data.
        64 bytes from 10.0.0.1: icmp_seq=1 ttl=63 time=188 ms
        64 bytes from 10.0.0.1: icmp_seq=2 ttl=63 time=197 ms
        64 bytes from 10.0.0.1: icmp_seq=3 ttl=63 time=188 ms
        
        --- 10.0.0.1 ping statistics ---
        3 packets transmitted, 3 received, 0% packet loss, time 2002ms
        rtt min/avg/max/mdev = 188.389/191.265/196.962/4.027 ms
        ```
