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
