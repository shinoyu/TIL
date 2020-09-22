# PCとモバイルでのクリックイベントをどう扱うか？

https://spyweb.media/2017/12/25/js-touch-click-event-twice-at-the-same-time/

2020年の現在でもclickとtouchstartを使い分けなければ行けない模様。
イベント設定用のwrapperを作ってそちらを使うように設定しないといけない


# classのインスタンスメソッドを参照する際の落とし穴

イベントハンドラの中でのthisはインスタンス自体をささない。そのままだと参照エラーで処理に失敗する。

```
// bad 
element.addEventListener('event', method);

// good
element.addEventListener('event', (e) => method(e));
```

この書き方にすることでようやく、thisでメソッド中でインスタンスの変数、関数にアクセスすることができる
