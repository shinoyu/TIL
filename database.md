# 結局日付型のカラム名はどうつければいいのさ

Railsの場合、testとかfixtureでは〜_at、~_onが使われていることがわかる。実装だとここが参考になる
https://github.com/rails/rails/blob/b6a9d3abda47c42f54d05b76d1d6b253056c861c/activerecord/lib/active_record/timestamp.rb


Stackoverｆlowだと、 `I normally name DATETIME columns as` とある。(出典は不明)
https://stackoverflow.com/a/1379630


個人的な見解だけど、やはり~_at系で書いたほうがわかりやすい。ひと目でtimestampとわかる。
日付にする場合は_onを推奨。現場によっては_dateにすることもある。その現場の方針次第でどうするかを考えればよさそう。
