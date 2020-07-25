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
