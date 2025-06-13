# 作業履歴

## ✅ 結論（20250613時点の完全動作構成・実績）
「https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz をPotreeでWebブラウザ上に表示した」
- `examples/copc.html` をリポジトリルートに `index.html` としてコピーした
- すべてのパス（`<link>`/`<script>`/ESM import）をルート基準に修正した
  - ESM(import)は必ず `./` で始めるようにした（例: `import * as THREE from "./libs/three.js/build/three.module.js";`）
- `libs/`, `build/`, `workers/` をルート直下に配置した
- `.nojekyll` をルートに設置した
- `pointclouds/` は任意（外部URLのCOPC/EPTを使う場合は不要だった）
- サブモジュールや入子構造は一切不要だった

---

## 🔧 手順（実施内容・記録）

### 1. Potreeをビルドした

```bash
# Node.js 16以上が必要
node -v

git clone https://github.com/potree/potree.git
cd potree
git checkout tags/1.8.0
npm install
npm run build
```

### 2. ディレクトリ構成（GitHub Pages用に配置した）

```
potree-copc-viewer/
├── .nojekyll
├── index.html         # ← examples/copc.html をコピーし、パスを修正した
├── libs/
├── build/
│   └── potree/
│       ├── potree.js
│       ├── potree.css
│       └── resources/
├── workers/
└── pointclouds/       # 任意（外部データ利用時は不要だった）
```

### 3. index.html の修正ポイント（実施済み）

- すべてのパスをルート基準に修正した（`../` を削除）
- ESM(import)は必ず `./` で始めるようにした
  - 例: `import * as THREE from "./libs/three.js/build/three.module.js";`
- COPC/EPTのURLは外部でもローカルでもOKだった

### 4. .nojekyll の設置（完了）

```bash
touch .nojekyll
```

---

## ⚠️ 注意点（実際に遭遇したこと）

- **CORS**: 外部COPC/EPTファイルはCORS対応が必須だった（`Access-Control-Allow-Origin: *`）
- **サブモジュール/入子構造はNG**: ルート直下にすべて配置する必要があった
- **GitHub Pagesの設定**: developやmainなど、公開したいブランチのルートを指定した

---

## ✅ 成功の確認ポイント（実績）

- `https://<username>.github.io/<repo>/` でPotreeビューアが表示された
- DevToolsで404や猫画像が出なかった
- `potree.js` などが200で読めていた
- 指定COPC（[104811.copc.laz](https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz)）が正しく表示された

---

## 📚 参考
- [Potree GitHub](https://github.com/potree/potree)
- [Potree COPC対応PR](https://github.com/potree/potree/pull/1381)
- [CORS解説](https://developer.mozilla.org/ja/docs/Web/HTTP/CORS)
- [GitHub Pagesと.nojekyll](https://docs.github.com/ja/pages/using-jekyll-with-pages/about-jekyll-and-github-pages)

---

## 📝 実施計画と達成履歴（202506142200)

### Potree COPC Viewer 拡張計画（実施済み）

#### 目的（当初計画）
- UIにID入力ボックスを追加し、任意のCOPCファイル（例: 114112）を表示できるようにした。
- 表示中のCOPCのリンクをURLクエリパラメータ（?r=xxxx）で共有可能にした。
- GitHub Pages上で動作する静的Webアプリとして実現した。

#### 実装手順（実施内容）
1. `index.html`にID入力用のテキストボックスと「表示」ボタンを追加した。
2. ボタン押下時、入力値からCOPCファイルのURLを生成し、`window.location.search`を書き換えてページをリロードするようにした。
3. ページロード時、クエリパラメータ`r`があればその値でCOPCを表示するようにした。
4. 現在表示中のCOPCの共有用URLをUIに表示し、コピーできるようにした。
5. GitHub Pagesで動作するよう、外部リソースのパスやCORSに注意した。

#### 備考
- 入力値がURLの場合はそのまま利用し、IDの場合は既定のURLパターンで組み立てるようにした。
- 共有URLは`?r=xxxx`形式とした。
- 既存の`getQueryParam`や`path`のロジックを活用した。

##### COPC配信リンクのルール
- 例えば「104811」というIDが入力された場合、COPCファイルのURLは
  `https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz`
  の形式で組み立てて利用した。
