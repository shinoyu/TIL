## WSLで構築したサーバーに任意のホスト名でアクセスするときにやること

所謂/etc/hosts

こうした場合、ローカルでもWSL上サーバーと疎通できない
`127.0.0.1 domain`

WSLはWindows側とは別のIPアドレスを発行して動いているので、127.0.0.1にしても疎通ができない。
WSL起動時に、hostsのWSL ipアドレスを取り直して設定してくれる手段を使うと解決できそう。

https://nabinno.github.io/f/2019/07/06/WSL2%E6%99%82%E4%BB%A3%E3%81%AEDocker%E9%96%8B%E7%99%BA%E3%82%B9%E3%82%BF%E3%82%A4%E3%83%AB.html
