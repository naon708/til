## 【Linux】`apt`と`apt-get`の違い
| `apt` | インタラクティブ向けにカスタマイズしており、確認しながらの使用におすすめ(sshなど) |
|--------|--------|
| `apt-get` | CLIのみ対応しており、バックグラウンド動作におすすめ(シェルやdockerなど) | 

- どちらもDebian系のパッケージ管理システム

ref. https://maimai-tech.com/programming/terminal/apt-vs-aptget/

### memo
- 今回はDockerコンテナ内で直接インストールしたかったので`apt-get install`を使った
- `apt-cache search --names-only [パッケージ名]`でパッケージ名を調べておくと安心(パッケージ名とコマンドの名称が乖離しているケースが多いため)

---

## 【機械学習】トレーニング完了後のモデルをエクスポートした際に生成されたファイルについて
```bash
ll ./inference

-rw-r--r--  1 root root 405K Nov  6 08:50 inference.pdiparams
-rw-r--r--  1 root root  28K Nov  6 08:50 inference.pdiparams.info
-rw-r--r--  1 root root 378K Nov  6 08:50 inference.pdmodel
```

> これらのファイルはバイナリ形式で保存されている。
>
> テキストエディタで開いて内容を閲覧・編集するのではなく、特定のプログラムやAPI（機械学習フレームワークが提供するツール）に読み込ませて使用することが想定される。

- `inference.pdmodel`: モデルの構造(設計のようなもの)
- `inference.pdiparams`: モデルがデータから学習したパラメーター
- `inference.pdiparams.info`: 追加のメタデータを含んでおり、より詳細なパラメーターの情報

> これらのファイルは、モデルを訓練環境から本番環境へ移行する際や、異なるマシン、プラットフォーム、アプリケーションでモデルを利用する際に使います。
>
> 実際には、これらのファイルを機械学習フレームワークが提供する推論エンジンに読み込ませて、新しいデータに対する予測を行うために使います。

---

## 【機械学習】頻出用語に慣れたい
「トレーニング」がテスト勉強で「推論」が実際のテスト
