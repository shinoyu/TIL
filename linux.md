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
su xxx wheel

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

 
