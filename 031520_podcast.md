# アウトライン
1. イントロ
2. UIT Inside初参加者のchaserさんによる自己紹介
3. Vue 3勉強会の紹介 (おさらい)

4. chaserさんによるComposition APIの紹介
- https://github.com/vuejs/rfcs/blob/master/active-rfcs/0013-composition-api.md
- なぜComposition APIが導入されることになったのか
- 形式的なAPIの解説

5. @spring-rainingが実際にComposition APIを使って小さなアプリケーションを作る
- Vue 2 + @vue/composition-api の構成で試しました
- 作っていき感じたこと/利点/欠点
  - APIが色々増え覚えることが多そうと感じたが、作ってみると意外と大丈夫
  - mutableな値の最小単位として ref 、ref の副作用として watch があり、種々のAPIはそれぞれを使いやすくしたものという考え方
  - 欠点とかは特に思いつかなかった
- 「機能ごとにコードの断片がまとまりメンテナンスが高まる」という主張は正しいのか
  - 完全に正しかった、今まで computed methods などで分断していたロジックがまとまり書きやすい・読みやすい
- TSとの親和性
  - defineComponent を使ったり、Vue 3が提供する型に推論させれば何も問題なさそう
  - <template> 内の型が自由に効かないもどかしさ
    - render functionを使えば解決するかも
    - Vue 3を使えば解決するかも
  - Template ref周りが依然しんどい
    - https://vue-composition-api-rfc.netlify.com/api.html#template-refs
    - そもそも @vue/composition-api ではVue 2の制約で文字列でしか指定できない
    - 特に v-for と組み合わさった文法はかなり分かりづらい
<div
  v-for="(item, i) in list"
  :ref="el => { divs[i] = el }">
  {{ item }}
</div>

6. Reactとの比較
- Renderingの度に何度も関数が呼び出される、というHooksの動作は初心者にとって直感的でないのは確か
- 逆にReact Hooksはただの関数であることによってSimpleを保っている
- Composition APIにおける computed() は、Hooksではただの変数

7. Drawbacksの深堀り
- Ref vs Reactiveの概念
- 柔軟性が以前より増す代わりに品質を維持するのが大変なのでは？
- 柔軟性を「多様性・方向性」と考えることもできそう
- @spring-raining: 並のOSSなら分断が起きるかもしれないが、Vueならドキュメントやサードパーティのサポート等しっかりやってくれそう

8. @vue/composition-api について
- Vue 2でもComposition APIを使用可能になるプラグインだが、そもそもサポート対象なのか、どの期間までサポートされるのか？
- chaserさんがIssueで質問してくれた、そして返信がきた
- https://github.com/vuejs/composition-api/issues/282

9. Composition APIを使うべきか否か？
- さんざん議論されている命題だが、一応2人の立場としても表明しておきたい
- @spring-raining:
  - APIとして明らかに使いやすくなっているので積極的に使いたい
  - 使うならTypeScript、render functionと組み合わせたい
- chaserさん:
  - Vueの型サポートが弱いのはかなり苦しめられたので新規ならすぐさま使いたい
  - 既存の場合は利用しているpluginに破壊的修正が入る可能性が高いので要注意という形になりそう
10. クロージング

# メモ
2. UIT Inside初参加者の吉澤さんによる自己紹介
- 社内ではAbyss(S3 likeの物にuploadを行うcli)とBelchero(nodeのpaas。簡易heroku)という社内ツールの開発/メンテ/運用などをやっている
- フロントエンドの部署に所属しているがメイン業務がバックエンドやインフラという変わり種
- オフではRustをメインにやっている。wasmとして動くrust製簡易jvm作ったりとかrustfmtのバグをもりもり直したりとかしてた

3. 吉澤さんによるComposition APIの紹介
## summary
- 関数ベースのAPIを提供することで柔軟に実装ができる様になりましたよ
- 具体的には
a. setupという新規の関数が出来上がる
b. thisを使わない
c. dataの代わりやreactiveな値を使いたい場合はrefとreactiveを用いる

a. setupについて
- setupという関数が新規に提供される
- computedやonMountedなどのmethodsがvueのmoduleからimportできる
- template内で扱いたい値はreturnで返す
- mountedなどのライフサイクルを仕込みたい場合は、setup内で宣言する
b. thisを使わない
- propsとかどうするの？
  - setupの引数として2つ渡される
    - 第一引数: props
    - 第二引数: context
      - attrs
      - emit
      - slots
c. dataの代わりやreactiveな値を使いたい場合はrefとreactiveを用いる
- Refはプリミティブな変数
- Reactiveはオブジェクト
- data関数でreturnしたものは暗黙的にreactive関数を実行したものとして扱われている
This is the very essence of Vue's reactivity system. When you return an object from data() in a component, it is internally made reactive by reactive()

## Motivation
- 大規模プロジェクトでも使いやすくするためにコードの可読性やてスタビリティを上げたい
- ロジックの再利用がもっと便利にできる様にしたい
- 型のサポートを強化したい

# optionといっているもの
- components
- props
- data
- computed
- methods
- lifecycle methods

=> これはオプショナルなものというよりはロジックが散らばるということが問題の様

# reuse component
## 2.0だと3つあった
1. mixins
- name conflict                   名前の衝突
- unclear relationships           関係性が不明瞭
- not easily reusable             再利用しにくい
2. mixin factories
- Weak Namespacing                namespaceは相変わらず問題
- implicit property additions     プロパティの追加が暗黙的である
- No instance access at runtime   実行時にinstanceにアクセスできない
3. scoped slot
- increase indentation            インデントが増える
- lots of configuration           設定が多い
- less flexible                   柔軟性にかける
- less performant                 パフォーマンスに問題がある

4. composition functions
- advanced syntax                 新規に導入されたものである


### Better Type Inference
- thisへの依存を取り除きましょうね。そうすると色々な問題が解決しますよという感じがある
- this.$props とかその辺りを避懈怠
- class componentについて触れないと駄目


7. Drawbacksの深堀り
- Ref vs Reactiveの概念
- 柔軟性が以前より増す代わりに品質を維持するのが大変なのでは？
- 柔軟性を「多様性・方向性」と考えることもできそう
- @spring-raining: 並のOSSなら分断が起きるかもしれないが、Vueならドキュメントやサードパーティのサポート等しっかりやってくれそう

7. Drawbacksの深堀り
## ref vs reactive
- Refはプリミティブな変数
- Reactiveはオブジェクト
  - Reactiveはvue2系のVue.obseravableです。RxJS observablesと混じると良くないので改名したそうです
  - 注意が必要でdestructed, spreadするとReactiveの効果がなくなる
  - destructed, spreadしたい場合はtoRefsというAPIがあるので、それを使う
  - 意図的に分けたのは性能の問題か何か? ソースを読んだり測定してみると面白いかもね

- @vue/composition-api を使うことで当面はVue 2でもComposition APIを使用可能
  - 試験利用目的っぽい。少なくとも現状でmigrationの用途で使わせるつもりはなさそう
  - とりあえず質問のためにissue投げた
  - 移行に使えるレベルのものではあるが
    - パフォーマンスクリティカルなところに使う際は注意
    - 私はサイドプロジェクト以外でプロジェクト全体の移行に使ってはいない
    - 2020 Q1目標にmigration toolを作る予定はある。vueのprojectをみてね
