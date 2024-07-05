
当我配置ssh key时对于github可能使用某个，gitlab使用另一个，并且github需要代理访问

~/.ssh/myauth这个文件内容是代理的账号密码，没有使用可以使用注释掉那一行

```
username:password
```

```sh
Host github.com
  HostName ssh.github.com
  #ProxyCommand nc -X connect -x 43.x.xx.22:10080 %h %p
  ProxyCommand corkscrew 43.x.x.22 10080 %h %p ~/.ssh/myauth
  Port 443
  IdentityFile ~/.ssh/y

Host gitlab.co.jp gitlab.co.jp
  User git
  Port 2022
  IdentityFile ~/.ssh/x
```
