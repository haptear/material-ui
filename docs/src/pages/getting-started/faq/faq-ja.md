# よくある質問と回答

<p class="description">特定の問題で立ち往生していますか？ よくある質問でこれらのよくある問題のいくつかを最初に確認してください。</p>

それでも探しているものが見つからない場合は、[gitter](https://gitter.im/mui-org/material-ui)というコミュニティに質問することができます。 使い方の質問やその他のnon-issuesではない場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/material-ui) やGithub issuesの代わりに質問できます。 `material-ui`というStackOverflowタグがあります。 質問にはそのタグをつけてください。

## productionビルドでコンポーネントが正しくレンダリングされないのはなぜですか？

これは、コードがproduction bundleに入った後にクラス名が競合するために発生する可能性があるn°1の問題です。 Material-UIが機能するためには、`clsx`ページ上のすべてのコンポーネントの値は、[クラス名ジェネレータ](/customization/css-in-js/#creategenerateclassname-options-class-name-generator)の単一インスタンスによって生成される必要があります。

この問題を解決するには、ページ上のすべてのコンポーネントを初期化して、それらの間の**クラス名ジェネレータが1つだけ**存在するようにする必要があります。

さまざまなシナリオで、誤って2つのクラス名ジェネレータを使用することになる事例

- 誤ってMaterial-UIの2つのバージョンを**bundle**してしまっている場合、 依存関係がMaterial-UIを対の依存関係として正しく設定されていない可能性があります
- Reactツリーの**サブセット**に`JssProvider`を使用している場合
- bundlerを使っていて、それが複数のクラス名ジェネレータインスタンスが作成されるという方法でコード分割しています。 webpackで [SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)を使用している場合は、[`最適化環境下`で`runtimeChunk`を設定してみてください](https://webpack.js.org/configuration/optimization/#optimization-runtimechunk)

全体として、各Material-UIアプリケーションをコンポーネントツリーの最上部にある[`JssProvider`](/customization/css-in-js/#jssprovider)コンポーネントでWrapし、**コンポーネントツリー間で共有される単一のクラス名ジェネレータを使用することで**、この問題を簡単に解決できます。

解決の最後の手段は、使用しているbundlerによって異なりますが、全体的な解決方法は、上記の最初のスニペットを含む共通モジュールを確実に1回だけロードして実行することです。

⚠️緊急ですか？ 安心してください。クラス名を**明示的**にエスケープするために[`dangerouslyUseGlobalCSS`](/customization/css-in-js/#global-css)オプションを用意しています。

## モーダルを開くと、fixed positionされたDOMが移動するのはなぜですか？

モーダルが開かれるとすぐにスクロールをブロックします。 モーダルが唯一のインタラクティブなコンテンツであるべき場合、backgroundとの連動を防ぎます。しかし、スクロールバーを取り除くことで**fixed positionされたDOM**を動かすことができます。 この場合、Material-UIにこれらのDOMを処理するように伝えるために、グローバルな `.mui-fixed`クラス名を適用することができます。

## 波紋アニメーションをグローバルに無効にする方法は？

波紋アニメーションは、BaseButtonコンポーネントからのみ発生しています。 テーマに次のように指定することで、波紋アニメーションをグローバルに無効にすることができます。

```js
import { createMuiTheme } from '@material-ui/core';

const theme = createMuiTheme({
  props: {
    // Name of the component ⚛️
    MuiButtonBase: {
      // The properties to apply
      disableRipple: true, // No more ripple, on the whole application 
    },
  },
});
```

## アニメーションをグローバルに無効にする方法

テーマに次のように指定することで、アニメーションをグローバルに無効にすることができます。

```js
import { createMuiTheme } from '@material-ui/core';

const theme = createMuiTheme({
  transitions: {
    // Então temos `transition: none;` everywhere
    create: () => 'none',
  },
});
```

テスト中やローエンドデバイスなどで、条件付きでこの動作を有効にしたい場合があります。この場合は、テーマの値を動的に変更できます。

## アプリのスタイルを設定するにはJSSを使用する必要がありますか？

強くお勧めします。

- ビルトインのため、追加のbundleサイズのオーバーヘッドはありません
- 速い & メモリ効率が良い
- 明瞭で一貫性のある[API](https://cssinjs.org/json-api/)
- ネイティブでも[プラグイン](https://cssinjs.org/plugins/)でも、多くの高度な機能をサポートします。

しかし、おそらくすでに別のスタイルライブラリを使用してアプリケーションにいくつかのMaterial-UIコンポーネントを追加している、 またはすでに別のAPIを使用している場合には、新しいものを学びたくはないでしょう？ その場合は、[スタイルライブラリの相互運用](/guides/interoperability/)セクションで、Material-UIコンポーネントを別のスタイルのライブラリでスタイル変更することがいかに簡単であるかを示します。

## inline-styleもしくはclassesどちらを使うべきですか？

経験則として、動的styleプロパティにはinline-styleのみを使用してください。 CSSの代替手段は、次のようなより多くの利点を提供します。

- auto-prefixing
- デバックのしやすさ
- メディアクエリ
- keyframes

## react-routerの使い方は？

私達は[サードパーティ製ルーティングライブラリ](/demos/buttons/#third-party-routing-library)で`ButtonBase`コンポーネントの使い方をドキュメント化しました。 私たちのインタラクティブなコンポーネントの多くはButtonBaseコンポーネントを内部的に使っています：`Button`, `MenuItem`, `<ListItem button />`, `Tab`, などなど。 それらの例を参考にしてください。

## どうやって`withStyles()`と`withTheme()`をHOCで統合できますか?

さまざまなオプションがあります。

**`withTheme`オプション:**

```js
export default withStyles(styles, { withTheme: true })(Modal);
```

**`compose()`ヘルパー関数:**

```js
import { compose } from 'recompose';

export default compose(
  withTheme(),
  withStyles(styles)
)(Modal);
```

**生の関数チェーン：**

```js
export default withTheme()(withStyles(styles)(Modal));
```

## どうやってDOM要素にアクセスできますか？

コンポーネントを[`RootRef`](/api/root-ref/)ヘルパーでWrapします。

## 私が見ている色とこのサイトで見ている色が違うのはなぜですか？

ドキュメントサイトはカスタムテーマを使用しています。 したがって、カラーパレットがあるMaterial-UIが提供しているデフォルトのテーマは異なります。 テーマのカスタマイズについて学ぶには、この[ページ](/customization/themes/)を参照してください。

## Material-UIは最高です。 プロジェクトを支援するにはどのようにできますか？

Material-UIをサポートする方法はたくさんあります。

- [ドキュメント](https://github.com/mui-org/material-ui/tree/next/docs)を改善する 
- 他の人が始めるのを手伝う
- [ライブラリを布教する](https://twitter.com/MaterialUI) 
- [StackOverflowの質問](https://stackoverflow.com/questions/tagged/material-ui)、またはリポジトリで[質問としてマークされているissues](https://github.com/mui-org/material-ui/issues?q=is%3Aopen+is%3Aissue+label%3Aquestion)に答えてください。

商用プロジェクトでMaterial-UIを使用していて、スポンサーになることによってその継続的な開発を支援したい場合は、 あるいはサブや趣味のプロジェクトで**スポンサー**になりたい場合は、[OpenCollective](https://opencollective.com/material-ui)を使って行うことができます。

集められた資金はすべて透過的に管理され、スポンサーはREADMEとMaterial-UIのホームページで表彰されます。