# nokogiriのビルドエラーについて

mac環境だと一発で入ってくれない模様

ビルドの依存パッケージとして、 `libxml2` `libxslt` `libiconv`がそれぞれ必要になるので入れておくこと

bundlerで入れる場合は、オプションとしてシステムライブラリを参照するように設定をいれておく

```
bundle config build.nokogiri --use-system-libraries 
```

# mysql2 gemのsslライブラリ無し系エラー

brewでいれたライブラリは勝手に使われないようなので、ビルドオプションで設定を入れておく

```
bundle config --global build.mysql2 --with-opt-dir="$(brew --prefix openssl)"
```
