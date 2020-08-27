# DeployKey

サーバー側で作成した公開鍵を設定すると、ユーザーと同じような感じで認証通してくれるやつ。
キーの作り方はユーザーの公開かぎ登録と同じもので問題なかった。

作成した秘密鍵は、appサーバー増やすときに使い回すので、scpで取得して管理しておくこと。

# Github Actions

## use Repository Secrets

```
name: use secrets
run: |
  echo ${VALIABLE}
env:
  VALIABLE: ${{ secrets.VALIABLE }}
```

## use cache

bundle installとかあの手のファイルを一時的に永続化できる仕組み？

```
steps:
  - uses: action/cache@v2
    with:
      fetch-depth: 0
      ref: {branch}
```
ケースによるが、だいたいdepth0で事足りるので、指定入れておく。
refにブランチを入れる。

他にも色々オプションがある。https://github.com/actions/checkout
