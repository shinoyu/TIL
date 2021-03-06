# aws_instanceのvpc_securirty_group_idsを指定すると発生するエラーの対応

```
Error registering targets with target group: InvalidTarget: The following targets are not in the target group VPC
```
target groupが解決できないエラーが出る。
subnet_idの指定が無かったため、整合性がチェックできなかったから起きているものだと思われる。設定したらエラーが消えた.

# iam roleに紐付けるポリシー

iam_policyとiam_role_policyの2種類がある。
iam_policyはユーザー等で使うロール。instanceに使うのは、iam_role_policyと違いがある。


# instanceにiam roleがアタッチできない。

作成されたiam roleに Instance Profile Arnが設定されていないことが原因。
aws_iam_instance_profileでroleとの関連付けをしないといけない

```
resource "aws_iam_instance_profile" "ec2" {
  name  = "name"
  role = aws_iam_role.role.name
}
```

上記設定後、aws_instanceに以下を追加しておくと、インスタンスとiamの設定が紐付けられる
```
iam_instance_profile = aws_iam_instance_profile.profile.name
```

# EC2のパブリックDNSが設定されていない

aws_instanceのパラメータとしては、public_dnsが該当。だけとこれは不要だった。サブネットから解決されている気がする。
aws_vpcのオプションでenable_dns_hostnamesの指定をtrueにする必要がある。

https://www.khasegawa.net/posts/2017/10/public-dns-atatch-to-ec2-created/


# 特定のリソースだけ更新

`terraform plan -target {aws_resource}.{target}`

# aws_security_group - ingressのfrom, toポートの関係性

フォワーディングではなく、入ってくるポートの範囲。

# パスワードなどの秘匿情報の扱い

結局stateに設定した情報が入ってしまうので、S3などに置くとして、
terraform.tfvarsというterraform用の変数管理ファイルを用意して、それをgitにいれないように管理するのが良さそう。つまり結局は.envである。

stateに記録したくないなど厳密にやりたい場合は、仮値で設定してstateにいれておき、後でaws cliで変更に行くのが良さげ。
リソースを作った後、RDS設定にlifecycleを設定し、aws cliを浸かってパスワード上書きすればstateにも残らない。

```
# rds.tfに追加。terraformで管理しないパラメータであることを設定しておく
lifecycle {
  ignore_changes = [password]
}
```

```
# aws cliでパスワードの生成
aws secretsmanager get-random-password --profile '{aws-profile}'--require-each-included-type

# パスワードの設定
aws rds modify-db-instance --db-instance_identifier '{rds-name}' --master-user-password '{password}'
```

# 秘密鍵の設定

ローカルで作って公開鍵をアップロードする方針で実施した。以下設定。

```
resource "aws_key_pair" "{key-name}" {
  key_name = "{key_name}"
  public_key = vars.public_key
}

resource "aws_instance" "{name}" {
 key_name                    = "${aws_key_pair.{key-name}.key_name}"
```

# iam_role_policyをどう管理するか

大きく3つに分けて管理できると思われる。

1. 直接ヒアドキュメントで書く
2. data.aws_iam_role_policy_documentを使う
3. file(policy.json)にする

結論1で管理するのが良さそう。

設定内容が完全に固定なら、3が一番管理しやすいと思われる。そのままAWSで設定するのと同じフォーマットで管理できるのでAWSに慣れ親しんだ人なら使える。  
ただ、可変な文字列が入れられないため、Terraform側で作ったリソースと紐付ける情報が使えない。1を使った場合は可変の問題が解決できる。

2を使うケース。Terraform書式に合わせてかけるので、Terraform慣れている人は統一感が出るこちらを使うのが良さそう。


