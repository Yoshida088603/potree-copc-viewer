# ✅ 最終目的：指定COPCをPotreeでWeb表示する

このドキュメントの最終的なゴールは、**「https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz をPotreeでWebブラウザ上に表示する」**ことです。

- このCOPCファイルはCORS対応済みで、GitHub PagesやPotree公式ビューワー（[viewer.copc.io](https://viewer.copc.io/?copc=https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz)）からも直接アクセス可能です。
- 以下の手順・構成例は、**このCOPCをPotreeで正しく表示するため**のものです。

---

## ✅ 結論

GitHub Pages上でCOPC（Cloud Optimized Point Cloud）やEPT（Entwine Point Tile）をPotreeで表示するには：

- `examples/copc.html` をベースに `index.html` を作成
- 必要なディレクトリ（`libs/`, `build/`, `workers/`, `pointclouds/`）をルートに配置
- `.nojekyll` をルートに設置しJekyll処理を回避
- 外部COPCファイルの**CORS設定（Access-Control-Allow-Origin）**が許可されている必要あり

---

## 💡 背景と問題点

### 1. PotreeのCDN利用状況

| バージョン | CDN存在 | COPC対応 | 備考 |
|------------|----------|-------------|------|
| v1.7        | ✅ jsDelivr  | ❌ 非対応      | Potree is defined になるが COPC 未対応 |
| v1.8+       | ❌ 非公開   | ✅ 対応済       | `potree.js` がCDNに存在せず 404 エラーになる |

- **CDNからの読み込みで「Potree is not defined」になる原因は、CDN上にビルド済みJSがないため。**
- v1.8以降はGitHub Releaseやローカルビルドで入手する必要がある。

**出典:**
- https://github.com/potree/potree/releases
- https://github.com/potree/potree/issues/1033
- https://cdn.jsdelivr.net/gh/potree/potree@1.8.2/build/potree/potree.js → 404 Not Found

---

## 🔧 正しい構成手順（v1.8以降でCOPC/EPT表示）

### 1. Potreeをビルド

```bash
# 前提: Node.js 16 以上がインストールされていることを確認
node -v   # v16.x 以上を推奨

# Potree ソースを取得
git clone https://github.com/potree/potree.git
cd potree

git tag -l "1.8.*"
git checkout tags/1.8.0
npm install
npm run build
```

この結果、以下のファイル・ディレクトリが生成される：

* `build/potree/potree.js`
* `build/potree/potree.css`
* `build/potree/resources/`
* `build/potree/workers/`
* `libs/`（外部ライブラリ群）
* `examples/copc.html`（サンプルHTML）
* `pointclouds/`（サンプル点群データやEPTデータセットを格納）

### 2. GitHub Pages用ディレクトリ構成

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
└── pointclouds/       # サンプル点群データやEPTデータセットを格納
    ├── lion_takanawa.copc.laz
    └── lion_takanawa_ept_laz_14/
        ├── ept.json
        ├── ept-data/
        ├── ept-hierarchy/
        ├── ept-sources/
        └── ept-build.json
```

- `index.html` は `examples/copc.html` をコピーし、`../` を削除してルートから参照できるように修正
- `libs/` ディレクトリは必須（jQuery, three.js, proj4.js などの外部ライブラリ群）
- `build/potree/` 配下に potree.js, potree.css, resources/ を配置
- `workers/` もルート直下に配置
- `.nojekyll` をルートに設置
- `pointclouds/` にはサンプルのCOPCファイルやEPTデータセットを格納

### 3. `index.html` の例（COPC/EPT表示対応）

`examples/copc.html` をベースに、パスを修正して利用します。

- COPCファイル（.copc.laz）を直接指定可能
- EPTデータセット（ept.json）もURLパラメータで指定可能
- **最終目的のCOPCを表示する場合は、下記のようにURLを直接指定する**

```html
<!-- 例: index.html の一部抜粋（最終目的のCOPCを表示する場合） -->
<script>
  var path = "https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz";
  var name = "104811";

  Potree.loadPointCloud(path, name, function(e){
    viewer.scene.addPointCloud(e.pointcloud);
    viewer.fitToScreen();
  });
</script>
```

#### 【補足】pointclouds/ ディレクトリの使い方
- `pointclouds/lion_takanawa.copc.laz` … COPC形式の点群ファイル例
- `pointclouds/lion_takanawa_ept_laz_14/` … EPT形式の点群データセット例（`ept.json`がエントリーポイント）
- 外部のCOPC/EPTデータを使う場合は、URLを直接指定すれば `pointclouds/` 配下にファイルがなくてもOK

---

## ⚠️ 注意点

### CORS

* COPCファイルのURLが `Access-Control-Allow-Origin: *` を返す必要がある
* `https://gsrt.digiarc.aist.go.jp/...` はCORS対応済み → GitHub Pagesから読み込み可能

**確認方法:**

```bash
curl -I https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz
```

### Jekyll無効化

GitHub Pagesでは `.nojekyll` をリポジトリルートに置くこと：

```bash
touch .nojekyll
```

---

## ✅ 成功後の確認ポイント

* `https://<username>.github.io/<repo>/` にアクセス
* DevToolsのNetworkタブで `potree.js` が 200 で読めているか
* コンソールに `Potree is not defined` が出ないこと
* `.copc.laz` や `ept.json` が Rangeリクエストで取得できていること
* **最終目的のCOPC（[104811.copc.laz](https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz)）がPotreeで正しく表示されること**

---

## 📚 参考リンク

* Potree GitHub: [https://github.com/potree/potree](https://github.com/potree/potree)
* Potree COPC対応PR: [https://github.com/potree/potree/pull/1381](https://github.com/potree/potree/pull/1381)
* CORS確認: [https://developer.mozilla.org/ja/docs/Web/HTTP/CORS](https://developer.mozilla.org/ja/docs/Web/HTTP/CORS)
* GitHub Pagesと.nojekyll: [https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages#configuring-pages-to-serve-content-from-the-root-directory](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages#configuring-pages-to-serve-content-from-the-root-directory)
* Potree公式ビューワーでの表示例: [viewer.copc.ioで表示](https://viewer.copc.io/?copc=https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz)

---

## ✅ 最終的な推奨

| 目的                  | 推奨方法                                                                               |
| ------------------- | ---------------------------------------------------------------------------------- |
| GitHub PagesでCOPC/EPT表示 | Potreeをローカルビルド→`examples/copc.html`を`index.html`化→`libs/`・`build/`・`workers/`・`pointclouds/`配置→`.nojekyll`設定 |
