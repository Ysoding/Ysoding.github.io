
在远程服务器上，检查 `/etc/ssh/sshd_config` 文件中的 X11 转发设置：

```
X11Forwarding yes
X11DisplayOffset 10

```

`sudo systemctl restart sshd`

配置本地ssh配置方便vscode使用  
~/.ssh/config

```
Host your-remote-host
    ForwardX11 yes
    ForwardX11Trusted yes

```

连接到远程服务器需要  

```
export DISPLAY=localhost:10.0

xclock
```

也可以直接用ssh命令行

```
ssh -X user@hostname
```
