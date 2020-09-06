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
