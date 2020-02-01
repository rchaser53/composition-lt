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

# React Hooksとは何が違うの?

# Classコンポーネントってどうなったの?

# Svelteと比較してどうなの？

----

# reference
- https://vue-composition-api-rfc.netlify.com/
  - ありがたいことに別途ページが作られてます
  - 動画もついているので詳しく知りたい人はこちらをどうぞ