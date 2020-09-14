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
