# 結局日付型のカラム名はどうつければいいのさ

Railsの場合、testとかfixtureでは〜_at、~_onが使われていることがわかる。実装だとここが参考になる
https://github.com/rails/rails/blob/b6a9d3abda47c42f54d05b76d1d6b253056c861c/activerecord/lib/active_record/timestamp.rb


Stackoverｆlowだと、 `I normally name DATETIME columns as` とある。(出典は不明)
https://stackoverflow.com/a/1379630


主要DBのルールを見てみる
Oracle: https://docs.oracle.com/cd/B28359_01/server.111/b28310/tables003.htm#ADMIN11633
  dateをつける
MYSQL: https://dev.mysql.com/doc/refman/5.6/ja/creating-tables.html  
  カラム名には付けない
Postgres: https://www.postgresql.jp/document/9.3/html/sql-createtable.html
  dateを含める形。特にこれといったやり方は記載されていない
MongoDB: https://docs.mongodb.com/manual/core/document/
  Oracleルールに親しい印象

DBごとにルールが有るわけではなさそう。言語・フレームワークの文化によるものが大きいと思われる


個人的な見解だけど、やはり~_at系で書いたほうがわかりやすい。ひと目でtimestampとわかる。
日付にする場合は_onを推奨。現場によっては_dateにすることもある。その現場の方針次第でどうするかを考えればよさそう。
