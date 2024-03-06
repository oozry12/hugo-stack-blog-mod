---
title: 通过设置代理解决github不能正常连通
date: 2023-12-20T19:53:38+08:00
updated: 2023-12-20
tags:
    - github
    - proxy
---
突然发现无法使用 `git@github.com:user/repo` 推送或拉取代码了，之前明明可以。证明抽风了。解决方法只能上代理，晕啊。

## 方法一 创建 `~/.ssh/config`
```bash
Host github.com
    User git
    ProxyCommand connect -H 127.0.0.1:10809 %h %p
```

这是要求走http代理的设置，把端口改为你的代理端口

另一种是换成这个，强制走443端口利用https协议
```bash
Host github.com
 Hostname ssh.github.com
 Port 443
```

## 方法二 设置全局代理
`git config –global http.proxy protocol://127.0.0.1:port`

如

`git config --global http.proxy socks5h://127.0.0.1:1080`

但这个方法对我无效

## 参考ref
- [gist](https://gist.github.com/bluethon/45766d0a4541ab633e32a49b6c4e4bdd)
- [github doc](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-a-proxy-server-with-self-hosted-runners)
- [v2ex](https://v2ex.com/t/849046)