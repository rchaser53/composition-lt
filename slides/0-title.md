<!-- classes: title -->

# Composition APIについて
## 吉澤 峻行(rchaser53)

---

# kazupon氏の資料で済ませられるもの
## 大体以下の内容はkazupon氏の資料を読めば良い
### url: https://speakerdeck.com/kazupon/mamonakuyatutekuru-vue-dot-js-3?slide=22

- Motivation
- Detailed Design
- Drawbacks(欠点)の概要
- Adoption strategy(2系でどう使うの?)
  - 補足の資料がいると思う

---

# 今回やること
### 触れなかったことや掘り下げができそうな内容を話していく
- Drawbacks(欠点)
- Appendix(付録)

---

# Drawbacks
- Refを導入する上でのオーバーヘッド
- Ref vs Reactive
- setupのreturnする値が冗長では？
- 柔軟性が以前より増す代わりに品質を維持するのが大変なのでは？

---

# Refを導入する上でのオーバーヘッド
### thisへの依存をなくすためにrefを導入したがoverheadがある
- 通常のprimitiveな値やobjectなどと区別しないといけない
  - 変数への命名を上手くやって回避して欲しい
- 値の取得や変更に.valueを使わないといけないところが面倒
  - .valueを使わないようにするためのpluginを提供予定

---

# Ref vs Reactive
### Refは変数、Reactiveはオブジェクトの様なイメージ

```js
// style 1: separate variables(Ref)
let x = 0
let y = 0

function updatePosition(e) {
  x = e.pageX
  y = e.pageY
}

// style 2: single object(Reactive)
const pos = {
  x: 0,
  y: 0
}

function updatePosition(e) {
  pos.x = e.pageX
  pos.y = e.pageY
}
```

---
# Ref vs Reactive
### Reactiveはそのままではdestructedもできないし、spreadもできない

```js
// composition function
function useMousePosition() {
  const pos = reactive({
    x: 0,
    y: 0
  })

  return pos
}

// consuming component
export default {
  setup() {
    // reactivity lost!
    const { x, y } = useMousePosition()
    return {
      x,
      y
    }

    // reactivity lost!
    return {
      ...useMousePosition()
    }

    // this is the only way to retain reactivity.
    // you must return `pos` as-is and reference x and y as `pos.x` and `pos.y` in the template.
    return {
      pos: useMousePosition()
    }
  }
}
```

---
# Ref vs Reactive
### destructed, spreadしたい場合はRefでラップすること

```js
function useMousePosition() {
  const pos = reactive({
    x: 0,
    y: 0
  })

  return toRefs(pos)
}

// x & y are now refs!
const { x, y } = useMousePosition()
```

---

# setupのreturnする値が冗長では？
### ボイラープレートみたいで冗長に感じるかもしれないが…
- レンダリングコンテキストに公開するものを明示的に制御できる
- 処理を追っていくスタート地点にしやすい

---

# 柔軟性が以前より増す代わりに品質を維持するのが大変なのでは？
### 品質の上限は上がるが同様に下限も下がる
- 下がる下限は上限に比べてとても小さいからセーフ
- ドキュメントやコミュニティにガイダンスはしっかりやる
- Angular1系のcontrollerを想像した人がいたかもしれない
  - Vue3系は共有スコープのコンテキストには依存しないので大丈夫

---

# Appendix
- React Hooksとは何が違うの?
- Classコンポーネントってどうなったの?
- Svelteと比較してどうなの？

---

# Classコンポーネントってどうなったの?
- 元々の目的はTypeScriptの型推論のサポートが強化された代替APIを提供すること
- Genericsの利用も検討したが、宣言が冗長のため採用せず
- 代わりにdecoratorの利用を検討したがstage-2なので採用せず
- ktsn氏が面白いことを呟いていたが、どうなるだろう…
  - https://twitter.com/ktsn/status/1223807962763812865

---

# React Hooksとは何が違うの?
## 一回しか呼び出されない
- 一般的で直感的なコードが書ける
- 呼び出される順番にあまり気をつけなくて良いし、条件的にしやすい
- 何度も呼び出されないからGCが走る心配が少ない
- 考慮事項がReact Hooksと比較すると少ない
  - 考慮事項とは？

---

# React Hooksを使う上での考慮事項
- useCallbackの利用の有無
  - インラインハンドラーを書くと子コンポーネントが再レンダリングされてしまう
  - 回避するのにuseCallbackを使う
- useEffectとuseMemoを使った際の依存関係のハンドリング
  - 正しく処理をしないと古い値がキャプチャされてしまう
  - Vueのwatchとcomputedは、その辺りを勝手にやってくれる

---

# Svelteと比較してどうなの？
### コンポジションAPIとSvelte3は概念的にはかなり近いところがある

---

# vue

```js
<script>
import { ref, watch, onMounted } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      count.value++
    }

    watch(() => console.log(count.value))

    onMounted(() => console.log('mounted!'))

    return {
      count,
      increment
    }
  }
}
</script>
```

---

# Svelte

```js
<script>
import { onMount } from 'svelte'

let count = 0

function increment() {
  count++
}

$: console.log(count)

onMount(() => console.log('mounted!'))
</script>
```

---

# VueはJavaScriptの標準に従っている
## 近い点
- コンポーネントのインスタンスごとに呼び出される関数にラップされる(一度だけ実行されるのではなく)
- 暗黙的にリアクティブな変数として保存される
- スコープ内の全ての変数をレンダリングコンテキストに公開する
- $ステートメントを再実行コードにコンパイルする

---

# Vueと比較した際の欠点
- Sveleteはコンパイラを使用しないと実行できない事
- ロジックの一部を外部に抽出しようとすることができない点(svelte/store)
  - https://svelte.dev/docs#svelte_store
- 変数のリアクティビティはtopレベルでのみ使用でき、関数内では使用できない点
  - https://svelte.dev/repl/4b000d682c0548e79697ddffaeb757a3?version=3.6.2
- TypeScriptを使用する上で問題のあるsyntaxを使用してしまっていること
  - https://github.com/sveltejs/svelte/issues/1639

---

# reference
- https://vue-composition-api-rfc.netlify.com/
  - ありがたいことに別途ページが作られてます
  - 動画もついているので詳しく知りたい人はこちらをどうぞ

- https://speakerdeck.com/kazupon/mamonakuyatutekuru-vue-dot-js-3
  - これ見た方が早いのでは？

- https://github.com/uki213/LearningCompositionApi
  - composition apiを使ったexample repository
