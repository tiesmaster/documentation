# Datadogドキュメントの翻訳作業を進めるために [JP]

翻訳作業を進める上でインストールしておくと便利なツールの設定方法を解説します。

## textlintのインストール

翻訳テキスト表記の同一性を担保するためにtextlintを活用します。
インストール方法は以下にないます。

```
npm install -D textlint
```

<!-- @suppress InvalidSymbol -->
`package.json` ファイルがトップディレクトリにあることを確認して次のコマンドを実行します。

```
npm install
$(npm bin)/textlint --init
```

<!-- @suppress InvalidSymbol -->
`.textlintrc` ファイルを次の様になっているかを確認します。

```
{
  "filters": {
    "comments": true,
    "whitelist": true
  },
  "rules": {
    "no-todo": true,
    "preset-ja-spacing": true,
    "preset-ja-technical-writing": true,
    "preset-japanese": true,
    "preset-jtf-style": true,
    "terminology": true
  }
}
```

動作確認をします。

```
$(npm bin)/textlint README.jp.md
```

複数行に渡るerrorが出力された後、以下の様な行が出力されます。

```
✖ 56 problems (56 errors, 0 warnings)
✓ 8 fixable problems.
Try to run: $ textlint --fix [file]
```

## remarkのインストール

markdownのsyntax errorを防ぐためにremarkを活用します。

```
npm install -D remark
npm install -D remark-lint
npm install -D remark-cli remark-preset-lint-recommended
npm install -D remark-validate-links
```

<!-- @suppress InvalidSymbol -->
次に、package.josnの設定を追記します。

```
"scripts": {
    "lint-md": "remark ."
  },
  "remarkConfig": {
    "plugins": [
      "remark-preset-lint-recommended",
      "validate-links"
    ]
  }
```

動作を確認します。

```
$(npm bin)/remark content/ja/index.md
```

## redpenのインストール

日本語の記述スタイルをチェックするために、redpenを利用します。

Mac OS Xを使っている場合は、homebrewを使って、インストールをします。
ついでに、設定ファイルもコピーしておきます。

```
brew install redpen
cp /usr/local/Cellar/redpen/1.9.0/libexec/conf/redpen-conf-ja.xml .
```

動作確認をします。

```
redpen -c redpen-conf-ja.xml -L ja content/ja/index.md -r plain2
```

以下のような結果が表示されれば動作しています。

```
[2017-06-07 19:06:08.674][ERROR] cc.redpen.Main - The number of errors "19" is larger than specified (limit is "1").
```
