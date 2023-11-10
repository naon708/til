## 【Rails】`find_or_initialize_by`メソッド
```ruby
# activerecord/lib/active_record/relation.rb
module ActiveRecord
  class Relation
    # 指定したレコードがあればそれを返し、なければインスタンス生成
    def find_or_initialize_by(attributes, &block)
      find_by(attributes) || new(attributes, &block)
    end

    # 指定したレコードがあればそれを返し、なければレコード作成
    def find_or_create_by(attributes, &block)
      find_by(attributes) || create_or_find_by(attributes, &block)
    end
  end
end
```

---

## 【Rails】Railsアプリケーションにおける`attr_accessor`
```ruby
# 一時的なデータ保持やDBに保存しない値を属性として定義したいときに使う
attr_accessor :sync_result
```
- 今回だとrakeタスクのログ出力用の文章とかに使ってた
  - rakeタスク内のserviceクラスで値を入れて、serviceクラス外で参照したり

---

## 改行コードの種類(`LF`, `CRLF`, `CR`)
- macOS　→　`LF`
- Windows　→　`CRLF`

| 名称 | 意味 | コード | 向き | OS |
| --- | --- | --- | --- | --- |
| CR（Carriage Return） | カーソルを左端の位置に移動する。復帰。 | \r | ← | MacOS 9以前 |
| LF（Line Feed） | カーソルを次の行に移動する。改行。 | \n | ↓ | MacOS X以降 |
| CR + LF（Carriage Return + Line Feed） | カーソルを左端に移動して、次の行に移動する。復帰+改行。 | \r\n | ← + ↓ | Windows |

https://cprogram.net/line-feed-code/

### 背景
- Windowsで開発したコードを管理するGitリポジトリをmacOSで編集する際に、改行コード分の差分がでてしまう
- また、コードに`CRLF`と`LF`が混在しているとプログラムの動作やビルドに影響する
- なので、Pullしたコードを利用するだけなら問題ないが、編集するなら`LF`に置き換えたほうが良い

### スクリプトで更新できる
```bash
# 一時的に autocrlf を false にする
git config core.autocrlf false

# ワークツリーを更新する(.gitディレクトリを除いて消去し、チェックアウトしなおす)
ls -A --color=never -I .git | xargs -d '\n' rm -rf
git checkout -- .

# 改行コードを変換
git grep -l -I $'\r$' | xargs -d '\n' nkf -Lu --overwrite
```
ref. https://kokufu.blogspot.com/2017/03/git-repository-crlf-lf.html

---

## 【Docker】Dockerイメージ内とホストマシンは無関係(別領域)
### サンプルコマンド
```bash
docker container create --gpus=all -it --mount type=bind,src="/hoge",dst="/hoge" --name text_detection registry.baidubce.com/paddlepaddle/paddle:2.4.2-gpu-cuda11.7-cudnn8.4-trt8.4
```
### 前提
- コンテナは独立したLinux環境であり、必要なライブラリや依存関係を含んだイメージから作成される
- NVIDIAのCUDAやcuDNN、TensorRTなどのGPU関連のライブラリがプリインストールされている
- ホストとコンテナでファイルを共有している(マウント)

![docker_mount_arch](https://github.com/naon708/til/assets/77439261/0a443fcc-6be3-46dd-871b-023a1387152b)

### なぜイメージ内のライブラリがホストマシン上に存在しないか
- CUDAやcuDNNなどのライブラリはホストマシンに存在しない
- なぜなら、これらのライブラリはDockerのイメージに組み込まれているもので、コンテナ起動時にコンテナ内にロードされる
- これらのライブラリがDockerイメージ内に組み込まれており、コンテナが起動するときにそのイメージに基づいてライブラリがコンテナ内にロードされるため
- イメージに含まれるライブラリは、そのイメージから作成されたコンテナ内でのみアクセス可能

> Dockerコンテナが起動する際、コンテナは使用するDockerイメージに含まれているファイルシステムスナップショットをベースに実行されます。このイメージにはアプリケーションを実行するために必要なコード、ランタイム、ライブラリ、環境変数、コンフィグファイルなどが含まれています。
> 
> CUDAやcuDNNのようなライブラリは、イメージ作成時に特定のディレクトリにインストールされます。例えば、CUDAは通常 /usr/local/cuda ディレクトリ内にインストールされることが多いです。これらのライブラリはファイルとしてイメージ内に存在し、コンテナが起動するとコンテナのファイルシステムにマウントされます。

### 自分向け
ホスト-コンテナ間でマウントしてるディレクトリとライブラリがインストールされるディレクトリは別だったでしょ

---

## 【Rails】`TimeWithZone`を使おう
- datetimeだとActiveRecordで投げたときに値が変わってしまう

```ruby
Customer.where(created_at: time).to_sql
=> "SELECT \"customers\".* FROM \"customers\" WHERE \"customers\".\"deleted_at\" IS NULL AND \"customers\".\"created_at\" = '2024-01-23 10:00:00'"

Customer.where(created_at: datetime).to_sql
=> "SELECT \"customers\".* FROM \"customers\" WHERE \"customers\".\"deleted_at\" IS NULL AND \"customers\".\"created_at\" = '2024-01-23 19:00:00'"

Customer.where(created_at: timewithzone).to_sql
=> "SELECT \"customers\".* FROM \"customers\" WHERE \"customers\".\"deleted_at\" IS NULL AND \"customers\".\"created_at\" = '2024-01-23 10:00:00'"
```

```ruby
DateTime.new(2024, 1, 23, 10, 0)
=> Tue, 23 Jan 2024 10:00:00 +0000
# => DateTime class

Time.new(2024, 1, 23, 10, 0)
=> "2024-01-23T10:00:00.000+09:00"
# => Time class

Time.zone.local(2024, 1, 23, 10, 0)
=> Tue, 23 Jan 2024 10:00:00.000000000 JST +09:00
# => ActiveSupport::TimeWithZone class
```


ref. https://qiita.com/jnchito/items/cae89ee43c30f5d6fa2c#activesupporttimewithzone%E3%82%AF%E3%83%A9%E3%82%B9

---

## ChatGPTの回答を図解で理解する
```
1. ◯◯の処理フローを解説してください。また、PlantUMLで表してください
2. http://www.plantuml.com/plantuml にアクセスし、提示されたPlantUMLコードを図で確認する
3. 必要に応じてPNGやSVGでダウンロードする
```
http://www.plantuml.com/plantuml

```
bluegray
cerulean
```

---

## 【機械学習】モデルトレーニングの処理フロー

![機械学習_処理フロー_toy](https://github.com/naon708/til/assets/77439261/a10a6703-8124-4119-872c-b21720046548)

> 1. データの収集と前処理（Preprocessing）: 最初のステップでは、機械学習モデルをトレーニングするためのデータを収集し、前処理（クリーニング、正規化、変換など）を行います。
> 2. トレーニング（Training）: 収集されたデータを使用して、アルゴリズム（モデル）をトレーニングします。このステップでは、データのパターンを学習し、モデルのパラメータを最適化します。
> 3. 評価（Evaluation）: モデルの性能を評価するために、通常はトレーニングデータとは別のテストデータセットを使用します。評価はモデルが未知のデータにどのように一般化するかを測定します。
> 4. チューニング（Tuning）: 評価ステップで不十分な性能が見られる場合、モデルを調整（ハイパーパラメータの調整、アーキテクチャの変更など）して再び評価します。
> 5. 推論（Inference）/展開（Deployment）: モデルの性能に満足したら、新しいデータに対して予測や結果を得るために推論を行います。また、モデルを本番環境に展開して実際のアプリケーションで利用することもできます。

### OCRの場合はこんな感じ
データセット作成(アノテーション・ラベリング・水増し)<br>
↓<br>
トレーニング<br>
↓<br>
評価<br>
↓<br>
チューニング(データセット追加・configファイル変更)<br>
↓<br>
エクスポート(推論・展開)<br>

---

## コマンド解剖
```bash
tree -rif val | grep -E "jpg|JPG|jpeg|JPEG|png|PNG" | awk -F "/" '{print $0" "$2}'
```
```bash
# １行すべて表示したあと、２番めのフィールドをスペース区切りで表示
val/3/AC220525_002_0_0002.jpg 3
```
### `tree`
- `tree`: ディレクトリ内の構造をツリー形式で表示
- `-r`: ツリーを逆順（リーフからルートへ）に表示
- `-i`: インデントや枝を示す文字を非表示(ファイル名やディレクトリ名のみ表示)
- `-f`: フルパスでファイル名を表示

### `grep`
- `-E`: 拡張正規表現を使う
- `|`: 「または」の意味


### `awk`
- `awk`: テキストの検索・編集操作を行う
- `-F "/"`: スラッシュで分割
- `$0`: 1行すべて
- `" "`: スペース
- `$2`: `/`区切りの2番目の要素

あとで読む: https://qiita.com/hnishi/items/4ee60ed515470e796f41

---

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
