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

## syntax

### runs-onはJob個別に設定する

jobsの直下に指定して、全部のジョブで同じコンテナ使いまわしたかったけどダメ
```
# NG
jobs:
  runs-on: ubuntu-latest
  
# OK
jobs:
  task:
    runs-0n: ubuntu-latest
```

つまり、各処理毎の状態を持っていないと行けない場合は、1つのタスクの中で処理を完結させないと行けない。


## 原則コンテナを使って動くようにするべし

Github Actionsは時間で課金される形態なので、以下に実行時間を短くするかが重要になってくる。

例えばRubyの場合、ActionsでRubyを最初からインストールする場合とコンテナから起動する場合で比較すると、コンテナ数秒で上がるのに対しRubyインストールだと3、4分ほどかかる。
(https://github.com/actions/cache を使うことで多少は緩和される)
可能な限りコンテナで動作させるように組んでいくこと
