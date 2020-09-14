# Syntax

## options 

引数に値を渡すと、その値を使い分けた列挙型を作れる。enumにGenerics入れてみたようなもの、これかなり強力では。
```
pub enum　Result<T, E> {
  Ok(T),
  Ng(E)
}
```
OKの場合、T型の引数を要求できるようになる。NGの場合はE。それぞれ詰めたい物を可変にできるので、Stateみたいなものを用意して頑張らなくても良くなる。
パターンマッチとの組み合わせが凶悪そう。


# 気づき（後で整理するやつ)

## fileをopenしても閉じなくていい(勝手に閉じてくれる)

https://doc.rust-lang.org/std/fs/struct.File.html

サンプルコードでopen周りの処理をみていたんだけど、fileをcloseする処理がどこにも書かれていない。

https://www.reddit.com/r/rust/comments/38ka6i/how_to_close_a_file/
とあり、変数がスコープから外れてdropする際に勝手に閉じてくれるらしい。  
確かにfileという要素が予め分かっているのであればそう処理できそうではある。



## グローバルなところで定義したenumは、std::{enum}::{Enum}のような名前空間に配置される模様
`rustc 1.46.0 (04488afe3 2020-08-24)`

```
enum Result<T, E> {$
  Ok(T),$
  Err(E),$
}$

struct Context {$
  val: i32,$
  str: String,$
}

fn main() {
  let result: std::result::Result<i32, ::std::string::String> = Ok(200);$
  let val = Context{$
    val: 100,$
    str: "testa".to_string(),$
  };
}
```

Structで生成したものは、そのまま呼べる。Resultのenumは、std::result::Resultでないと利用できない。
enumは定義先が違うんだろうか。もう少し深堀りしたほうがいいかも。

# PapyrusというREPLを触ってみる
https://github.com/kurtlawrence/papyrus

```
[lib] papyrus=> let a = 1;
[lib] papyrus.> println!("{}", a)
1
papyrus [out3]: ()
[lib] papyrus=> let b = 10;
[lib] papyrus.> println!("{}", b)
1
10
papyrus [out4]: ()
[lib] papyrus=> println!("test")
1
10
test
papyrus [out5]: ()
```

なるほど。標準出力がスタックされているのか。
`runnable: papyrus can be run, without needing to manually handle repl states and output`
この話かな。便利な半面面倒臭さあるなぁ



ちなみにevcxr

```
Welcome to evcxr. For help, type :help
>>
>>
>> let a=1;
>> println!("{}", a)
1
()
>> let b = 10;
>> println!("{}", b)
()
10
>> println!("test")
test
()
```
