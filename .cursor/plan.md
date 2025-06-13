# ✅ 最終目的：指定COPCをPotreeでWeb表示する

このドキュメントの最終ゴールは、**「https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz をPotreeでWebブラウザ上に表示する」**ことです。

---

## ✅ 結論（2024/06時点の完全動作構成）

- `examples/copc.html` をリポジトリルートに `index.html` としてコピー
- すべてのパス（`<link>`/`<script>`/ESM import）をルート基準に修正
  - ESM(import)は必ず `./` で始める（例: `import * as THREE from "./libs/three.js/build/three.module.js";`）
- `libs/`, `build/`, `workers/` をルート直下に配置
- `.nojekyll` をルートに設置
- `pointclouds/` は任意（外部URLのCOPC/EPTを使う場合は不要）
- サブモジュールや入子構造は一切不要

---

## 🔧 手順（最短・確実版）

### 1. Potreeをビルド

```bash
# Node.js 16以上が必要
node -v

git clone https://github.com/potree/potree.git
cd potree
git checkout tags/1.8.0
npm install
npm run build
```

### 2. ディレクトリ構成（GitHub Pages用）

```
potree-copc-viewer/
├── .nojekyll
├── index.html         # ← examples/copc.html をコピーし、パスを修正
├── libs/
├── build/
│   └── potree/
│       ├── potree.js
│       ├── potree.css
│       └── resources/
├── workers/
└── pointclouds/       # 任意（外部データ利用時は不要）
```

### 3. index.html の修正ポイント

- すべてのパスをルート基準に修正（`../` を削除）
- ESM(import)は必ず `./` で始める
  - 例: `import * as THREE from "./libs/three.js/build/three.module.js";`
- COPC/EPTのURLは外部でもローカルでもOK

### 4. .nojekyll の設置

```bash
touch .nojekyll
```

---

## ⚠️ 注意点

- **CORS**: 外部COPC/EPTファイルはCORS対応必須（`Access-Control-Allow-Origin: *`）
- **サブモジュール/入子構造はNG**: ルート直下にすべて配置すること
- **GitHub Pagesの設定**: developやmainなど、公開したいブランチのルートを指定

---

## ✅ 成功の確認ポイント

- `https://<username>.github.io/<repo>/` でPotreeビューアが表示される
- DevToolsで404や猫画像が出ない
- `potree.js` などが200で読めている
- 指定COPC（[104811.copc.laz](https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz)）が正しく表示される

---

## 📚 参考
- [Potree GitHub](https://github.com/potree/potree)
- [Potree COPC対応PR](https://github.com/potree/potree/pull/1381)
- [CORS解説](https://developer.mozilla.org/ja/docs/Web/HTTP/CORS)
- [GitHub Pagesと.nojekyll](https://docs.github.com/ja/pages/using-jekyll-with-pages/about-jekyll-and-github-pages)
