# Connectについて


ユーザーからサービス提供者に支払い、プラットフォーム側で手数料を取るモデルを作る手段。
https://qiita.com/y_toku/items/7bfa42793801dfc5415d

アカウントの種別は3つ

- Standard
- Express
- Custom


Expressはシェアリングエコノミーで活用するもの。
本人の身分証明がないなど、簡易的に支払い機能を提供したい場合に利用することができる。

Standardは、提供者側が自分でStripeアカウントを作って管理してもらう必要がある。またダッシュボードはStripeで提供される
プラットフォームに載せた自社ストアを、「Stripeがわかる」運営が管理する時に適切。

Customは自ら必要なものを取捨選択していく。
認証フローなどプラットフォーム側で独自実装していく必要がある。


必要としている要件だと
- ストアは自分でStripeのアカウントをつくらなくてよい

の観点のため、Customを選択する必要がある。

## Connectアカウントとcapability

https://stripe.com/docs/connect/account-capabilities

Connectアカウントの作成時に聞かれる設定。2020/09/06時点の日本をターゲットにしていて、出ている選択肢だと2つ

- Transfers
- Card payments

### Transfers

プラットフォームで集金して事業者に送金するケースで使う。
UberEatsとかこのスキームな気がする。


### Card pamynets

カスタマーが事業者に支払いを直接するパターン。ECサイト等の手数料モデルを使っている場合だとこちらに該当する。


# stripe SDK

Rubyの場合はこれ。
https://github.com/stripe/stripe-ruby

起動時の設定をいれておく
```
Stripe.api_key = '{secret_key}'
Stripe.log_level = Stripe::LEVEL_INFO
```

# 実装時に参考になる情報

https://qiita.com/y_toku/items/7bfa42793801dfc5415d

ほぼこれ見れば解決する。


# Elementを用いたカードトークンの取得と、決済手段の保存について

https://qiita.com/y_toku/items/7e51ef7e69d7cbbfb3ca

Elementで生成したトークンを用いてCustomerオブジェクトを作成し、保存しておく。
トークンをDB保持するイメージ

このとき利用したトークンはあくまで登録までの一時情報で、カード情報を引くためのトークンではない。  実際、カード情報取得するAPIでは、トークンでではなくCustomerIDと、カードIDが要求される。

Elementから受け取ったときに`Stripe::Customer.create`を実行する。このときのリクエストに`{source: token}`を積むと内部で`Stripe::Card.Create`を行ってくれるらしく、カード情報も一緒に生成してくれる。レスポンスに`default_source`パラメータが付与されて帰ってくるので、これをカードの問い合わせトークンとして保存しておく。

https://stripe.com/docs/api/customers/create#create_customer-source



## 複数のカードを登録したときの挙動について

TODO


# 顧客情報の更新

Customerオブジェクトには、電話番号やメールアドレスといった連絡先情報を持っているものがある。
サービス側のユーザー情報を変更する際、すでに決済手段が登録されているのであれば、この連絡先情報も更新してあげる必要がある。
https://stripe.com/docs/api/customers/create


