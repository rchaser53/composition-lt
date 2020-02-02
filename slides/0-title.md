<!-- classes: title -->

# Composition APIについて
## 吉澤 峻行(rchaser53)

---

# 動機
- コードの再利用 & コードの編成(Code Organization)をしやすくする
  - mixinやHOCより良いものを作ろう
- 型付けをしやすくする
  - thisへの依存がマジしんどい

# 実際のコードを見てみる
```vue
<template>
  <button @click="increment">
    Count is: {{ state.count }}, double is: {{ state.double }}
  </button>
</template>

<script>
import { reactive, computed } from 'vue'

export default {
  setup() {
    const state = reactive({
      count: 0,
      double: computed(() => state.count * 2)
    })

    function increment() {
      state.count++
    }

    return {
      state,
      increment
    }
  }
}
</script>
```

# 詳細

# setup is 何?
- ここで従来のdataやmethods, coputedなどを宣言する

# apiについての説明をアレコレ

# コードの編成(Code Organization)って結局何が言いたいの？

# ロジック抽出と再利用

# 既存のAPIと一緒に使いたい人へ

# Pluginの開発している人がやるべき事 + 注意すべき事


# 欠点
# Refを導入する上でのoverhead

# Ref vs Reactive

# setupが冗長になって結局管理しにくいのでは？

# 自由度が以前より増すために品質が結局下がるのでは？ 割れ窓


# 付録
- React Hooksとは何が違うの?
- Classコンポーネントってどうなったの?
- Svelteと比較してどうなの？

# Classコンポーネントってどうなったの?
- ktsn氏が面白いこと言ってたよ
  - https://twitter.com/ktsn/status/1223807962763812865

# React Hooksとは何が違うの?
- 一回しか呼び出されない
  - 一般的で直感的なコードが書けるよ
  - 呼び出される順番にあまり気をつけなくて良いし、条件的にしやすいよ
  - 何度も呼び出されないからGCが走る心配が少ないよ
  - インラインハンドラーを書くと子コンポーネントが再レンダリングしちゃうから大体の場合でuseCallbackが必要になるということがなくなるよ
  - ユーザーが正しい依存関係配列を渡すのを忘れた場合、useEffectとuseMemoが古い変数をキャプチャする可能性があるという問題を考慮する必要がない。 Vueは自動化された依存関係の追跡により、ウォッチャーと計算値が常に正しく無効化してくれる

# Svelteと比較してどうなの？
- コンポジションAPIとSvelte3のコンパイラのアプローチは概念的にはかなり近いところがある

- scriptタグに関して
  - コンポーネントインスタンスごとに呼び出される関数にラップされる(一度だけ実行されるのではなく)
  - 暗黙的にリアクティブな変数として保存される
  - スコープ内の全ての変数をレンダリングコンテキストに暗黙的に公開する
  - $ステートメントを再実行コードにコンパイルする

- VueはJavaScriptの標準に従っているけどSveleteはそうではない
1. Sveleteはコンパイラを使用しないと実行できない事
2. ロジックの一部を外部に抽出しようとすることができない点(svelte/store)
  - https://svelte.dev/docs#svelte_store
3. 変数のリアクティビティはtopレベルでのみ使用でき、関数内では使用できない点
  - https://svelte.dev/repl/4b000d682c0548e79697ddffaeb757a3?version=3.6.2
4. TypeScriptを使用する上で問題のあるsyntaxを使用してしまっていること
  - https://github.com/sveltejs/svelte/issues/1639


----

# reference
- https://vue-composition-api-rfc.netlify.com/
  - ありがたいことに別途ページが作られてます
  - 動画もついているので詳しく知りたい人はこちらをどうぞ

- https://speakerdeck.com/kazupon/mamonakuyatutekuru-vue-dot-js-3
  - これ見た方が早いのでは？

