# Logrotate

ログでディスクを使い潰さないために考えないといけないやつ。

基本形

```
compress
missingok
dateformat .%Y%m%d
copytruncate
delaycompress
notifempty
dateyesterday
su {user} wheel

/var/www/app/log/app.log {
    ifempty
    daily
    rotate 5
}
```

- compress
  - rotateしたファイルを圧縮するかどうか。常に有効で良いと思われる。
- missingok
  - 対象ファイルが存在しなくてもエラー扱いにしない。ケースによるが基本許可しておいていいと思われる
- dateformat
  - rotateされたログに付けるタイムスタンプの形式を指定するやつ
- copytruncate
  - rotateでは通常現在のログファイルをrotateして、新しく同名で作成するが、ログファイルを開きっぱなしにするプログラム走らせてると継続してrotateした方に出力されてしまう。
  - copytruncateは、コピーをrotateとして扱い、ログファイル自体はそのまま継続するので、この問題が解決できる。
  - 常に有効にしておいた方が余計なトラブルは防げるかも
- delaycompress
  - rotate時に圧縮せず、次のrotate時に実行するように遅延させる
  - copytruncateと同じく、ログファイル開きっぱなしのときはrotateした後も残ってしまうので、その時点で圧縮かけちゃうと書き込めなくなるため
  - copytruncate書いてたら不要そうだが、常に有効にしておいた方がトラブルは防げそう
- notifempty
  - ログファイルが空の場合はrotateしないようにする。ケースによるが、有効にしなくてもいい気がしている。
- dateyesterday
  - rotateファイルの日付をrotate日ではなくrotate対象日に変える。これを書かないとrotateファイル名と実際の期間が一致しなくなる。
  - 常に有効にしておくべき
- hourly, daily, weekly, monthly, yearly
  - rotateの間隔。アプリケーションのログに対してやる場合はdailyがよさそう
- rotate
  - いくつ残すか。
- su
  - rotateの実行ユーザーを指定する。
  - `id -u -n`実行時のユーザー名とグループを指定しておく

## 変更の反映

設定値にエラーが無いかを確認。
`logrotate -dv /etc/logrotate.conf`
logrotate.confは中でlogrotate.dのincludeしているので、このファイルを指定してやるのが確実。
 
このとき、以下のようなエラーが発生していないかチェックする
```
error: stat of /var/log/nginx/access.log failed: Permission denied
considering log /var/log/nginx/error.log
error: stat of /var/log/nginx/error.log failed: Permission denied
```
アクセス権限が不足していることで発生することが多い。該当のログのowner権限を確認し、それに合わせて`su`パラメータを設定すること

`logrotate /etc/logrotate.conf`で反映


# SSH

## フォワーディング設定の踏み台にも鍵指定必要

踏み台を使うシチュエーション。それぞれで鍵が設定されている場合、ProxyCommand側でも-iオプションを付けないと通らない

local -> bastion -> server

```
ssh -i key -oProxyCommand='ssh -i key -W %h:%p bastion' server
```

普通に考えたら当たり前の話で、異なるキーを使う運用もあるため、当然それぞれで設定が必要になる。
同じキーを使う場合でも暗黙的に読み替えてくれるわけではない。明示的に指定しておくこと。
