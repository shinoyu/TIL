# ファイルの書き込み権限でのオープン

io.fs.OpenOptionsを使う。  
https://doc.rust-lang.org/std/fs/struct.OpenOptions.html

```rust
use std::fs::OpenOptions;

// readonly
let file = OpenOptions::new().read(true).open("file-name");

// ファイルが無かったら作成して、書き込み読み込み権限与えたパターン
// ここには書いてないけどappend、truncateもある
let file = OpenOptions::new()
             .read(true)
             .write(true)
             .create(true)
             .open("file_name")

```

# tailみたいに監視したい

スターの多そうなライブラリの動きを見てみる。

https://github.com/aravindavk/logwatche

調べた限り、rust自身はfileのwatchメソッドを持っていない。
なので、loopで回して以下のように実装するのが良いかと思われる

- ファイルを読み込んで、現在のファイルサイズとposを記録
- loopしてWaitしながらファイルのmetadataを読む
- メタデータのlen()を読んでサイズが変わっていたら、実際にファイルを読んで処理

このまま実装した時に起きることとして、ファイルに削除などの追加以外の変更が加えられた時。削除とか。
となると、保持しているposと実際のファイルの位置だったものが一致しなくなる。

tailコマンドは編集を特に検知しないようになっているし、多分データを削除してなにか追記しても、もともとのposを超えた分しか画面に出さないのでは？の仮説が考えられる。


Ubuntu20.xで実際に試したところ、`echo test >> file`で追記し続けてるときはちゃんと追従する。しかしvimで編集した後同じように追記すると、差分が拾われない。その後何回か追記してもダメなので、前ちゃんと処理したposから再開はしない。一度posがマッチしなくなったら監視を止めるようになっている模様。BSDのコードで、そこまでちゃんと見れてないけどこの辺がそうかな
https://svnweb.freebsd.org/base/head/usr.bin/tail/forward.c?view=markup#l421

となると、rustでも同じように前よりファイルが縮んでいたら止める。もしくはその時点の終端にseekして、次のループを待つ、みたいな実装でそれっぽいことが作れそうイメージ

