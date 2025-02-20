# 遅い計算

変更検知のサイクルごとに、Angular は同期しています。

- 各コンポーネントの検知戦略に基づいて、特に指定がない限り、すべてのコンポーネントのすべてのテンプレート式を評価します。
- `ngDoCheck`、`ngAfterContentChecked`、`ngAfterViewChecked`、および `ngOnChanges` のライフサイクルフックを実行します。
Angular はこの計算を順番に実行するため、ひとつのテンプレートまたはライフサイクルフック内の遅い計算が、変更検知プロセス全体を遅くする可能性があります。

## 遅い計算の特定

Angular DevTools のプロファイラーを使用して、負荷の高い計算を特定できます。パフォーマンスタイムラインで、特定の変更検知サイクルをプレビューするためにバーをクリックします。これは棒グラフを表示し、フレームワークが各コンポーネントの変更検知に費やした時間を示します。コンポーネントをクリックすると、Angular がそのテンプレートとライフサイクルフックの評価に費やした時間をプレビューすることができます。

<div class="lightbox">
  <img alt="Angular DevTools profiler preview showing slow computation" src="generated/images/guide/change-detection/slow-computations.png">
</div>

たとえば、上のスクリーンショットでは、プロファイラーが起動してから 2 回目の変更検知サイクルを選択したところ、Angular は 573 ミリ秒以上費やしました。Angular は`EmployeeListComponent`にもっとも時間を費やしました。詳細パネルでは、`EmployeeListComponent`のテンプレートの評価に 297 ミリ秒以上費やしていることがわかります。


## 遅い計算の最適化

遅い計算をなくすためのテクニックはいくつかあります。

- **基礎となるアルゴリズムの最適化。**これは推奨されるアプローチです。問題の原因となっているアルゴリズムを高速化できれば、変更検知メカニズム全体を高速化できます。
- **純粋なパイプを使用したキャッシュ。**重い計算を純粋な[パイプ](/guide/pipes)に移動させることができます。Angular は、純粋なパイプを前回呼び出したときと比較して、入力が変化したことを検知した場合にのみ、再評価を行います。
- **メモ化の利用。**[メモ化](https://ja.wikipedia.org/wiki/%E3%83%A1%E3%83%A2%E5%8C%96)は純粋なパイプと同様の技術ですが、純粋なパイプが計算の最後の結果のみを保存するのに対し、メモ化は複数の結果を保存することができるという違いがあります。
- **ライフサイクルフックでのリペイント/リフローの回避。**特定の[操作](https://web.dev/avoid-large-complex-layouts-and-layout-thrashing/)により、ブラウザはページのレイアウトを同期的に再計算するか、再レンダリングします。リフローとリペイントは一般に低速なので、変更検知のたびに実行するのは避けたいものです。

純粋なパイプとメモ化には異なるトレードオフがあります。関数の結果をキャッシュする一般的なソフトウェアエンジニアリングのプラクティスであるメモ化に対して、純粋なパイプは Angular の組み込みの概念です。重い計算を異なる引数で頻繁に呼び出すと、メモ化のメモリオーバーヘッドは大きくなる可能性があります。

@reviewed 2022-05-04
