# アウトライン
## イントロ
1. UIT Inside初参加者の吉澤さんによる自己紹介
2. Vue 3勉強会の紹介 (おさらい)
3. 吉澤さんによるComposition APIの紹介
  -  https://github.com/vuejs/rfcs/blob/master/active-rfcs/0013-composition-api.md
  - なぜComposition APIが導入されることになったのか
  - 形式的なAPIの解説
4. @spring-rainingが実際にComposition APIを使って小さなアプリケーションを作る
  - 作っていき感じたこと/利点/欠点
    - (月曜に書く)
  - 「機能ごとにコードの断片がまとまりメンテナンスが高まる」という主張は正しいのか
    - (月曜に書く)
  - TSとの親和性
    - (月曜に書く)
  - Reactとの比較
    - Renderingの度に何度も関数が呼び出される、というHooksの動作は初心者にとって直感的でないのは確か
    - 蛇足かも
5. Drawbacksの深堀り
  - Ref vs Reactiveの概念
  - 柔軟性が以前より増す代わりに品質を維持するのが大変なのでは？
  - 柔軟性を「多様性・方向性」と考えることもできそう
  - @spring-raining: 並のOSSなら分断が起きるかもしれないが、Vueならドキュメントやサードパーティのサポート等しっかりやってくれそう
  - @vue/composition-api を使うことで当面はVue 2でもComposition APIを使用可能
6. Composition APIを使うべきか否か？
  - さんざん議論されている命題だが、一応2人の立場としても表明しておきたい
  - @spring-raining: (月曜に書く)
  - 吉澤さん:
- クロージング

# メモ
1. UIT Inside初参加者の吉澤さんによる自己紹介
- 社内ではAbyss(S3 likeの物にuploadを行うcli)とBelchero(nodeのpaas。簡易heroku)という社内ツールの開発/メンテ/運用などをやっている
- フロントエンドの部署に所属しているがメイン業務がバックエンドやインフラという変わり種
  - この辺アピールすべきか悩ましい。発言の説得力は減る可能性がある
  - LINEのUITの紹介という意味では非常に意義があると思われる

- オフではRustをメインにやっている。wasmとして動くrust製簡易jvm作ったりとかrustfmtのバグをもりもり直したりとかしてた

3. 吉澤さんによるComposition APIの紹介
## summary
- a set of additive, function-based APIs that allow flexible composition of component logic.

## Motivation
- Logic Reuse & Code Organization
  - Over the years we have witnessed some of these projects run into the limits of the programming model entailed by Vue's current API
  1. The root cause is that Vue's existing API forces code organization by options, but in some cases it makes more sense to organize code by logical concerns.
  2. Lack of a clean and cost-free mechanism for extracting and reusing logic between multiple components. (More details in Logic Extraction and Reuse)
    => Instead of being forced to always organize code by options, code can now be organized as functions each dealing with a specific feature
    => The APIs also make it more straightforward to extract and reuse logic between components, or even outside components. 


- Better Type Inference
=> thisへの依存を取り除きましょうね。そうすると色々な問題が解決しますよという感じがある
=> class componentについて触れないと駄目


## detailed designに触れるか否か
- reactive is the equivalent of the current Vue.observable() API in 2.x, renamed to avoid confusion with RxJS observables.
- When you return an object from data() in a component, it is internally made reactive by reactive().

- watchEffect is similar to the 2.x watch option, but it doesn't require separating the watched data source and the side effect callback. Composition API also provides a watch function that behaves exactly the same as the 2.x option.


## kazupon的
- 大規模開発ができるが問題があった
  - コンポーネントが大きくなるとコードが読みにくくなる
  - テストがしにくい
  - コード再利用のパターン化がしにくい
  - TypeScriptによる型のサポートが受けにくい
- composition apiを使うと
  - ロジックを自由度高く組み合わせ実装することができる
  - ロジック構成が綺麗になりテストもしやすくなる

5. Drawbacksの深堀り
- @vue/composition-api を使うことで当面はVue 2でもComposition APIを使用可能
  - the primary goal of this package is to allow the community to experiment with the API and provide feedback before it’s finalized
  - We do not recommend using this package for production yet at this stage.
    - 試験利用目的っぽい。少なくとも現状でmigrationの用途で使わせるつもりはなさそう
    - とりあえず質問のためにissue投げた

6. Composition APIを使うべきか否か？
