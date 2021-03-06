## match 式

前節では `match` 式をみました。

```scala
count match {
  case 0 => Image.empty
  case n => aBox beside boxes(n-1)
}
```

この新しい種類の式をどう理解して、自分でも書けるようになるでしょうか?
分解してみましょう。

まず最初に言っておくべきなのは、`match` が式であることで、そのためこれは値へと評価されます。
そうじゃなければ `boxes` メソッドはうまくいきません。

それが何に評価するのかを理解するには、もう少し詳細が必要です。
`match` 式は一般的に、以下のような形を持ちます:

```scala
<anExpression> match {
  case <pattern1> => <expression1>
  case <pattern2> => <expression2>
  case <pattern3> => <expression3>
  ...
}
```

`<anExpression>` (上記の具体例だと `count`) は、私たちがマッチをする値を評価するのに使われる式です。
パターン `<pattern1>` などのパターンはこの値に対してマッチングされます。
これまで 2種類のパターンをみました。

- リテラル (例えば `case 0`) は、リテラル式が評価される値と厳密にマッチし、
- ワイルドカード (例えば `case n`) は、**何にでも**マッチして右辺の式で使えるバインディングを導入します。

最後に、右辺の式 `<expression1>` などは、今まで書いてきたのと同じただの式です。
`match` 式全体は**最初**にマッチしたパターンの右辺式の値へと評価されます。
そのため、`boxes(0)` を呼ぶと両方のパターンとマッチしますが (ワイルドカードは何にでもマッチするため)、リテラルパターンが最初に来るため評価されるのは `Image.empty` の方です。

全ての可能な場合をチェックする `match` 式は、網羅的マッチ (exhaustive match) と呼ばれます。
`count` が 0以上であると前提を置けば、`boxes` の `match` は網羅的です。

まずは `match` 式に慣れてください。続いて自然数に対する構造的再帰の説明をする前に自然数の構造を見ていきます。

### 練習問題 {-}

#### 結果を予想する {-}

以下の式の評価値を予想して理由を考えることで match を理解しているか確かめてみましょう。

```tut:silent
"abcd" match {
  case "bcde" => 0
  case "cdef" => 1
  case "abcd" => 2
}
```

```tut:fail:silent
1 match {
  case 0 => "zero"
  case 1 => "one"
  case 1 => "two"
}
```

```tut:fail:silent
1 match {
  case n => n + 1
  case 1 => 1000
}
```

```tut:fail:silent
1 match {
  case a => a
  case b => b + 1
  case c => c * 2
}
```

<div class="solution">
第1の例は `2` に評価されます。それは、候補の中でパターン `"abcd"` が唯一リテラル式 `"abcd"` にマッチするものだからです。

第2の例は `"one"` に評価されます。最初にマッチした case が評価に使われるからです。

第3の例は `2` に評価されます。`case n` は何にでもマッチするワイルドカードパターンを定義するからです。

最後の例は `1` に評価されます。最初にマッチした case が評価に使われるからです。
</div>

#### マッチ無し {-}

`match` 式のどのパターンにもマッチしなかった場合はどうなるのでしょうか?
まずは結果を予想してみて、次にマッチに失敗する `match` 式を書いてみて正しく予想できたか実験してみましょう。
(現時点では特定の振る舞いをする論理的な理由は見当たらないので、常識の範囲内でどんな予想でもいいです)

<div class="solution">
私は 3つの常識的な可能性を思いつきましたが、あなたは他のアイディアがあるかもしれません。

- 式は、`Image.empty` のような何らかのデフォルト値へと評価することができるかもしれません。(どうやって Scala は正しいデフォルト値を選べばいいでしょうか?)
- Scala コンパイラはそのようなコードを禁止するべきです。
- `match` 式は実行時に失敗します。

マッチしない `match` 式の例です。

```tut:fail:book
2 match {
  case 0 => "zero"
  case 1 => "one"
}
```

正しい答は最後の 2つのどちらかで、コンパイルに失敗するか実行時に失敗します。
この例では、実行時の失敗となります。
正確な答はどう Scala が設定されているかによります (私たちは Scala に網羅的では無い `match` 式を拒否するように指示することができますが、それはデフォルトのふるまいではありません)。
</div>
