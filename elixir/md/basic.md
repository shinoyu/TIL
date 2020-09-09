# 基本的な使い方

## プログラムの実行
exsファイルにコードを書いて、以下で動かす。
```
elixir {file.path}
```



## iex

### 自作モジュールをロードする

```
iex> c("{file.path}")
```

やっていることはelixirコマンドと同じく、コンパイルして実行しているだけ。



## debug print

単に文字列ならIO.putsが使える。いわゆる普通のprint文。文字列以外を食わせるとArgumentError.  
文字列以外の場合一度文字列に変換しないといけなく、面倒なので、inspectを使う。

```
inspect value
```

IO.inspectが本体。長い配列等を指定すると途中までしか表示してくれない。
オプションで表示数を変更できる

```
# options 

:limit 
  collection系で有効。表示できる要素数。:infinityですべて表示
:printable_limit
  主に文字列で有効。表示できる要素数。:infinityですべて表示
:width 
  表示する文字数を指定する。:infinityですべて表示
:binaries
  バイナリ値の表示をどうやるか指定できる
    as_strings: 文字列として表示
    as_binaries: bit値として表示
    infer: String.printable?/2の値によってよしなにする。デフォルト値
　example)
   iex(8)> inspect "日本語", binaries: :as_strings
      "\"日本語\""
    iex(9)> inspect "日本語", binaries: :as_binaries
      "<<230, 151, 165, 230, 156, 172, 232, 170, 158>>"

```

# 型や演算子

## Binaryの定義

バイナリデータを表す値がある。この記法は結構珍しい
```
binary = << 1, 2 >>
```

## 演算子

```
a === b:
  厳格な同値
a !== b
  厳格な一致の逆
a == b
  一致
a != b
a < b
a <= b
```

PHPのように厳密な同値を扱えるようになっている

# 変数

## with式

スコープを限定するもの。他言語だとusingなどの概念が近しいかも。
elixirはwith式にもパターンマッチ書ける。色々と使えそうな所が多い印象ある 

# 関数

## 無名関数

```
sum = fn (a, b) -> a+b end
sum.(1,3)
```

無名関数を使うには、.を付けて呼び出す。
他言語だと無名関数と関数呼び出しの違いが区別できないが、Elixirなら判別つくのでよさげ

