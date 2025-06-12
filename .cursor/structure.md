# Potree COPC Viewer GitHub Pages 公開用ディレクトリ構造

このドキュメントは、GitHub PagesでPotreeとCOPCを表示するための推奨ディレクトリ構造を定義します。
`examples/copc.html` をベースとして利用することを前提とします。

```
potree-copc-viewer/  (リポジトリルート)
│
├── .nojekyll               # 【必須】GitHub PagesでJekyllを無効化する空ファイル。
│
├── index.html              # 【編集必須】メインのHTMLファイル。
│                           #  └─ (元ファイル: /examples/copc.html)
│                           #     これをコピーし、CSS/JSのパスから `../` を削除する修正が必要。
│
├── libs/                   # 【必須】Potreeが依存する外部ライブラリ群 (jQuery, three.jsなど)。
│   │                       # `index.html` から多数のファイルが参照されるため、ディレクトリごと必須。
│   └── ...
│
├── build/                  # 【必須】Potreeのビルド成果物を格納するディレクトリ。
│   │
│   └── potree/
│       ├── potree.js       # PotreeのメインJSファイル。
│       ├── potree.css      # PotreeのメインCSSファイル。
│       └── resources/      # アイコンなどのリソースファイル。
│
└── workers/                # 【必須】バックグラウンド処理用のWeb Workerスクリプト群。
    │
    └── ...

```

## セットアップ手順の概要

1.  **`index.html`の作成**:
    - `examples/copc.html` をリポジトリのルート (`/`) にコピーし、`index.html` にリネームします。
2.  **`index.html`のパス修正**:
    - コピーした `index.html` を開き、`<link>` タグと `<script>` タグの `href` や `src` に含まれる `../` (例: `../build/potree/potree.css`) をすべて削除します (例: `build/potree/potree.css`)。
3.  **必須ディレクトリの配置**:
    - `libs/`, `build/`, `workers/` ディレクトリが、`index.html` から参照可能なように、リポジトリのルートに正しく配置されていることを確認します。
    - `examples/copc.html` の記述に基づくと、`potree.js` 等は `/build/potree/` にある想定です。もし現在のプロジェクトで `/potree/` ディレクトリを使っている場合は、`index.html` のパスをそれに合わせて修正するか、ファイルを `/build/potree/` に移動する必要があります。
4.  **`.nojekyll`の作成**:
    - リポジトリのルートに空の `.nojekyll` ファイルを作成します。

## 各ディレクトリの役割補足

- **`examples/`**: Potreeの様々な機能を示すサンプルHTML群が格納されています。今回はこの中の `copc.html` を、GitHub Pagesで公開するためのベースとして利用します。公開するリポジトリにこのディレクトリを含める必要は必ずしもありませんが、他の機能を確認する際の参考になります。
- **`pointclouds/`**: サンプルの点群データが格納されています。`index.html` のデフォルトでは `../pointclouds/lion_takanawa.copc.laz` を読み込みにいきます。外部のCOPCファイルURLを指定する場合は、このディレクトリは不要です。

## 対応データ形式について（COPCとEPT）

`index.html` (元の`examples/copc.html`) は、Potreeの強力なローダー機能により、複数の形式の点群データを扱えます。

- **COPC (`.copc.laz`)**: 単一のCOPCファイルを直接URLで指定して読み込めます。これが基本的な使い方です。
- **EPT (Entwine Point Tile)**: `ept.json` ファイルをエントリーポイントとするEPT形式のデータセットも読み込み可能です。`pointclouds/lion_takanawa_ept_laz_14` ディレクトリの存在がこれを示唆しています。`index.html`内のスクリプトは、URLクエリパラメータを解釈して、EPTデータセットを動的に読み込むロジックも持っています。

このため、公開したいデータがCOPCかEPTかによって、`index.html` で指定するパスを変更することで、どちらの形式にも対応できる柔軟性があります。
