# Credentials

秘匿すべき環境変数等を暗号化して管理できる仕組み。Rails5.2から入り、Rails6からstage毎のキー管理ができるようになった、

## 鍵管理をどうするべきか

方法は2つ
- config/credentials/{stage}.key を配置
- RAILS_MASTER_KEY 環境変数に設定

自動デプロイ等を考えると、ファイル直置きは考えることが増えてしまう。
例えば、Github Actions使うときに、このキーを外部からダウンロードする処理を書く必要があるが、外部サービスのAccess keyを書くことになり、秘匿しておかなければ行けない情報が増えてしまい、大変よくない(とはいえ、githubの場合はRepository Secretに書けばいいが)

RAILS_MASTER_KEYを活用したほうが楽。Dockerとの親和性も高い。

## config/credentials/:environment.yml.sec が読めない

developmentでは再現せずstagingでのみ発生。  
ファイル自体は存在している。


以下で正しく設定値が確認できるため、キーが無いわけではない。
```
bundle exec rails credentials:show --environment staging
```

設定的には間違いなくパスが一致している。

```
[1] pry(::Application)> config.credentials
=> {:content_path=>
  #<Pathname:/var/www/app/config/credentials/staging.yml.enc>,
 :key_path=>
  #<Pathname:/var/www/app/config/credentials/staging.key>}
  }
```

しかし、rails cを叩くと、

```
validate_secret_key_base': Missing `secret_key_base` for 'staging' environment, set this string with `rails credentials:edit` (ArgumentError)
```

と言われてしまう。

結論、credentilas/:environment.yml.enc 側にsecret_key_baserというパラメータを追加する必要がある。master.keyと同様の値を設定しないと、キーの検証で弾かれる模様。

development環境は上記がなくても動く。この挙動の違いに苦戦...

# hashのキーはシンボルにしておくれ

ということを守ってくれない人がいるときに使う技。
ざんねんなことにハッシュの値を利用している側の実装が文字列前提で動いているのが確定的に明らかなので、文字列をシンボルにする過程でエラーとなり、背教者に「影響大きいのでシンボルにしません」とか言われがちになります。

それを回避するために、シンボル化したところを`with_indifferent_access`とする。
https://api.rubyonrails.org/classes/ActiveSupport/HashWithIndifferentAccess.html

こうすることでシンボルでも文字列でもちゃんとアクセスできるようになり、エラーを回避できる。
いい話。


# Railsのautpload_paths


一覧を確認したいとき
```
# for Zeitwerk
puts ActiveSupport::Dependencies.autoload_paths
```
