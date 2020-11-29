# mix

パッケージ管理ツール。Rubyでいうところのgemとかbundlerに相当する。


# プロジェクト作成

```
mix new {project name}
```

project nameでフォルダが作成され、中に最低限のファイルが生成される。


## フォルダ構成

```
├── lib
│   ├── {project name}/
│   └── {project name}.ex
├── mix.exs
├── README.md
└── test
    ├── {peoject_name}_test.exs
    └── test_helper.exs
```

フォルダ構成は規約で決まっている。コードはlib配下に実装していく。ネームスペースとディレクトリ構造は一致させる必要がある。

```
/assets   <- web assets
/config   <- configuration between prod, dev and test
/lib      <- My code
/private  <- Files to include in a release
/test     <- Test cases
```

http://dougo-chris.com/elixir-project-structure.html


## コマンドライン
