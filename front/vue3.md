# Vue.js 3.0
https://speakerdeck.com/kazupon/mamonakuyatutekuru-vue-dot-js-3

##  CompositionAPI

reactiveでstateを作ることで、state中のパラメータを変更したときに、Watchで指定したfunctionがコールされる。
computedで、リアクティブの状態の取得できるrefオブジェクトを取得でき、.valuedでアクセスできる。


computedは実行した結果をキャッシュしているが、computedの中で触っているstateのオブジェクトが更新されてると、キャッシュを捨てて新しい値を返却できるようになる。
https://github.com/vuejs/vue-next/blob/07919e00658592ebdb42f0c6f004f631c4bf4d34/packages/reactivity/src/computed.ts

2020/08/25現在
```ts
  get value() {
    if (this._dirty) {
      this._value = this.effect()
      this._dirty = false
    }
    track(toRaw(this), TrackOpTypes.GET, 'value')
    return this._value
  }
  
  # effect
  # 多分このへんで変更検知をしていると思われる
  this.effect = effect(getter, {
    lazy: true,
    scheduler: () => {
      if (!this._dirty) {
        this._dirty = true
        trigger(toRaw(this), TriggerOpTypes.SET, 'value')
      }
    }
  })
```

https://github.com/vuejs/vue-next/blob/07919e00658592ebdb42f0c6f004f631c4bf4d34/packages/reactivity/src/effect.ts#L178-L187
```
  const effects = new Set<ReactiveEffect>()
  const add = (effectsToAdd: Set<ReactiveEffect> | undefined) => {
    if (effectsToAdd) {
      effectsToAdd.forEach(effect => {
        if (effect !== activeEffect) {
          effects.add(effect)
        }
```

影響範囲の要素を示すオブジェクトを管理している。これで変更されたときに登録されているeffect要素を総ざらいして、get value時に要素の変更を検知して値の再更新をかけていると思われる？
