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
│                           #     これをコピーし、CSS/JSのパスから `../` を削除し、ESM(import)は必ず ./ で始めるよう修正。
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
    - ESモジュール（import文）は必ず `./` で始めるように修正します。
      例: `import * as THREE from "./libs/three.js/build/three.module.js";`
3.  **必須ディレクトリの配置**:
    - `libs/`, `build/`, `workers/` ディレクトリが、`index.html` から参照可能なように、リポジトリのルートに正しく配置されていることを確認します。
    - `potree.js` 等は `/build/potree/` にある想定です。
4.  **`.nojekyll`の作成**:
    - リポジトリのルートに空の `.nojekyll` ファイルを作成します。

## 各ディレクトリの役割補足

- **`examples/`**: Potreeの様々な機能を示すサンプルHTML群が格納されています。今回はこの中の `copc.html` を、GitHub Pagesで公開するためのベースとして利用します。公開するリポジトリにこのディレクトリを含める必要は必ずしもありませんが、他の機能を確認する際の参考になります。
- **`pointclouds/`**: サンプルの点群データが格納されています。`index.html` のデフォルトでは外部URLのCOPCファイルを参照するため、必須ではありません。

## 対応データ形式について（COPCとEPT）

`index.html` (元の`examples/copc.html`) は、Potreeの強力なローダー機能により、複数の形式の点群データを扱えます。

- **COPC (`.copc.laz`)**: 単一のCOPCファイルを直接URLで指定して読み込めます。これが基本的な使い方です。
- **EPT (Entwine Point Tile)**: `ept.json` ファイルをエントリーポイントとするEPT形式のデータセットも読み込み可能です。`index.html`内のスクリプトは、URLクエリパラメータを解釈して、EPTデータセットを動的に読み込むロジックも持っています。

このため、公開したいデータがCOPCかEPTかによって、`index.html` で指定するパスを変更することで、どちらの形式にも対応できる柔軟性があります。

## 2025/06 拡張アップデート実施記録

### 実施内容（@plan.mdより転記・過去形）
- 複数のCOPC ID（例: 104811, 104815, 104819 など）を一度に入力し、まとめて処理・表示できるようにした。
- ID入力ボックスを画面右上から右下に移動し、UIの使い勝手を向上させた。
- 使い方ガイドのURLを明示的にUI右上へ表示した。

#### 実装手順（実施済み）
1. ID入力欄でカンマ区切り、または改行区切りで複数IDを受け付けるようにした。
2. 入力値をパースし、各IDごとにCOPCファイルのURLを生成した。
3. 複数のCOPCを同時にPotree上で表示できるようにした。
4. 既存のID入力ボックスを右上から右下にレイアウト変更した。
5. スタイルシート（CSS）やHTMLの該当箇所を修正した。
6. 画面右上に「使い方」へのリンク（[使い方](https://note.com/yoshida_shirou/n/n61f55a71a56d)）を分かりやすく表示した。
7. ユーザーが迷わずアクセスできるよう、リンクの配置や文言を工夫した。
8. 複数ID入力時の動作、UI位置変更、使い方リンクの表示を確認した。
9. 共有URLやGitHub Pagesでの動作も再度検証した。

#### 備考
- 複数ID入力時、無効なIDや重複IDの扱いも考慮した。
- 使い方ガイドの内容は随時アップデートしている。
