# インスタンスタイプの選び方

https://pages.awscloud.com/rs/112-TZM-766/images/C2-07.pdf

基本はMを中心に使うとよい。必要な要件によって使うものを選ぶ。  
インスタンス作成あともインスタンスタイプ自体の変更ができるので、必要になったときに選んで行けばよいらしい

T系は安定したCPUを提供するより、バーストを使って処理量を増やす考え方。常に負荷が高いサーバーで利用するとCPUクレジットを使い果たして逆に詰まる。意識せず使うことを避けること。  
開発環境、マイクロサービスでの利用が推奨されている。

# private subnetにIGWを紐付けてNatゲートウェイ不要でoutboundできるらしい

https://qiita.com/SSMU3/items/5ed5792e74266b54ff8b

```
インスタンスにグローバルIPを設定する必要がある
```

private subnetだけど実質public networkと一緒なのでは。セキュリティグループで対策はできるが、サーバー増えてきたときはかなりしんどい。
やはり予めNat Gateway設定しておいた方が良さそう。


# ACMパラメータ

ACMをSDKで触るときに指定するオプション周りの整理

name|param|-
---|---|---
idempotency_token| string | requestCertificationのときにユーザーが任意でつけられる名前。重複申請判定などで使えるかもしれない。
certificate_authority_arn| arn | プライベート認証局のARN。いわゆるオレオレ証明書を作るときに使う。この指定がないとプライベート証明書が作れずパブリックとして作られてしまう。普通にAWSを運用している場合プライベートCAで証明書を発行する必要性はほとんどない？ので使うことはなさそう


# AWSでbastion環境を作る

## 必要なセキュリティグループの作成

1. bastion接続用のセキュリティグループを作成する。(bastion_sg)
  - inbound
     - 22: 0.0.0.0/0     # 一時的なSSHポート番号
     - {ポート}: 0.0.0.0/0  # 本来のSSHポート番号
2. bastion接続先のセキュリティグループを作成する(bastion_accept_sg)
  - inbound
    - 22: 1で作成したセキュリティグループ
    - icmp: 1で作成したセキュリティグループ

## インスタンス作成＋初期設定
    
EC2でmicroなbastionサーバーを構築する。
- elastic ipを設定
- bastion_sgを紐付ける

EC2でアプリケーション動かすサーバーを構築する
- bastion_accept_sgを紐付ける

## ホスト名設定


Route53のパブリックホストゾーンに、bastion.{domain}をAレコードで追加する
- 設定値はbastionサーバーのelastic ipから払い出されたIPアドレス

プライベートホストゾーンに、{任意}.{domain}をAレコードで追加する
- 設定値は、EC2のコンソールで確認できるインスタンスのプライベートIP

SSHでbastion.{domain}に入り、bastion上からpingをプライベートホストゾーンに追加したFQDNで投げて疎通を確認する。

インスタンス自身のシステムホスト名も合わせておく(取り間違いを防ぐため）
Route53のパブリックドメインの設定がある場合はFQDNで入れる。（bastionのみ該当)
```
sudo hostnamectl set-hostname webserver.mydomain.com
```

パブリックドメインの指定がない場合.localdomainにする

```
sudo hostnamectl set-hostname webserver.localdomain
```


## アクセスユーザーの作成

プログラム動かすグループと、操作ユーザーをBastion、対象サーバーともに作成する

```
groupadd {group}

# ユーザー作成
sudo adduser {user}
sudo su - {user}
# 公開鍵の取り込み 
curl -o ~/.ssh/authorized_keys --create-dirs https://github.com/{user}.keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

sudo gpasswrd -a {user} {group}
```

## InstanceConnectの設定




## bastionのSSHポートの変更
