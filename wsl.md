## WSL2で構築したサーバーに任意のホスト名でアクセスするときにやること

所謂/etc/hosts

こうした場合、ローカルでもWSL2上サーバーと疎通できない
`127.0.0.1 domain`

WSL2はWindows側とは別のIPアドレスを発行して動いているので、127.0.0.1にしても疎通ができない。
https://nabinno.github.io/f/2019/07/06/WSL2%E6%99%82%E4%BB%A3%E3%81%AEDocker%E9%96%8B%E7%99%BA%E3%82%B9%E3%82%BF%E3%82%A4%E3%83%AB.html

WSL2側のeth0のIPが接続先になる。まずはWSL側のIPアドレスを取得しておく。

```
# ip addr | grep eth0 
4: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    inet xxxx.xxx.xxx.xxx/20 brd xxx.xxx.xxx.xxx scope global eth0
```

このIPアドレスをWindows側のHostsに記載する

```C:\Windows\System32\drivers\etc
xxx.xxx.xxx.xxx domain
```

