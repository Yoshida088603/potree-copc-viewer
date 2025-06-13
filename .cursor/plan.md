# Potree COPC Viewer 拡張計画

## 目的
- UIにID入力ボックスを追加し、任意のCOPCファイル（例: 114112）を表示できるようにする。
- 表示中のCOPCのリンクをURLクエリパラメータ（?r=xxxx）で共有可能にする。
- GitHub Pages上で動作する静的Webアプリとして実現する。

## 実装手順
1. `index.html`にID入力用のテキストボックスと「表示」ボタンを追加する。
2. ボタン押下時、入力値からCOPCファイルのURLを生成し、`window.location.search`を書き換えてページをリロードする。
3. ページロード時、クエリパラメータ`r`があればその値でCOPCを表示する。
4. 現在表示中のCOPCの共有用URLをUIに表示し、コピーできるようにする。
5. GitHub Pagesで動作するよう、外部リソースのパスやCORSに注意する。

## 備考
- 入力値がURLの場合はそのまま利用、IDの場合は既定のURLパターンで組み立てる。
- 共有URLは`?r=xxxx`形式。
- 既存の`getQueryParam`や`path`のロジックを活用する。

### COPC配信リンクのルール
- 例えば「104811」というIDが入力された場合、COPCファイルのURLは
  `https://gsrt.digiarc.aist.go.jp/3ddb-pds/copc/104811.copc.laz`
  の形式で組み立てて利用する。
