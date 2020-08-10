# config/credentials/:environment.yml.sec が読めない

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
