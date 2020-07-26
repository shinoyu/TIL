# hochoが良さげ

https://qiita.com/k0kubun/items/f6a5ccc649a25fc61351

docker全盛期な今、そこまでガッツリとスクリプトを書くことがなくなった。
書くとしても大方Capistranoでやることがメインだし、Chefより簡単に使えるものがあるのは嬉しい。

## SSH設定

hosts.ymlに書く
```
{hostname}:
  ssh_options:
    user: '{user}'
    keys: ["{key_path}]
```

## 必要なパッケージ

- openssh
- rsync

dockerで導入する場合は使えるようにしておく
