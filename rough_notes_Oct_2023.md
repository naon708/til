## 【Python】コマンドライン引数を解析する関数
```py
# 慣例的にparse_argsとすることが多い
def parse_args():
    # docstring: 関数やクラス、モジュールなどのドキュメンテーション
    """Parse input arguments."""
    desc = ('説明文...')
    # argparse: コマンドライン引数の解析をサポートするモジュール
    parser = argparse.ArgumentParser(description=desc)

    # コマンドライン引数の定義
    parser.add_argument('-s_arg', '--some_argument', type=str, required=True, default=None, help='引数の説明')
    
    args = parser.parse_args()
    return args
```
```bash
$ python3 foo.py --tmp_labels bar.txt
```
### `--help`オプションで引数のdescriptionを確認できる
```bash
python3 <file name> --help
usage: text_renderer_train_val_maker.py [-h] -t_labels TMP_LABELS

optional arguments:
  -h, --help            show this help message and exit
  -s_arg SOME_FILE, --some_argment SOME_FILE
                        引数の説明
```

---

## Dockerコンテナ内のvimでyankした際、クリップボードにコピーされなかった
- Vimにも種類があって、クリップボードと連携できるものとできないものがある

> - vim-tiny(デフォルトでインストールされている)
> - vim
> - vim-gtk ◎
> - vim-athena ◎
> - vim-gnome ◎
> - vim-nox
>
> このうちクリップボードを利用できるものは◎の付いているvim-gtk、vim-athena、vim-gnomeになります。
この中から好きなものをapt-getしましょう。

https://sy-base.com/myrobotics/vim/vim_use_clipboard/

---

## `.ttf`ファイル (TrueType Font)
- フォントファイル
  - テキストの表示方法(= 各文字がどのように見えるか)を定義するグラフィック情報を持つ(各文字の形、大きさ、太さなど)
  - フォントファイルの内容を視覚的に確認したい場合は、フォントビューアやフォントエディタを使用する(vimだとバイナリデータとして解釈されるため文字化けする)

---

## GASデプロイ時のアクセス権限設定に注意
- SlackのEvent Subscriptionsを有効にするためにエンドポイントの検証が必要だった
- Slack側から検証のためGASのエンドポイントにアクセスを試みるが、GASデプロイ時のアクセス権限を「自分のみ」に設定していたためVerifiedできず詰まった

```
curlでアクセス
↓
「現在、ファイルを開くことができません。</p><p>アドレスを確認して、もう一度試してください」みたいなのが返ってくる
↓
デプロイ時のアクセス権限設定を「全員」に変更して解決
```

---

## Deadlock
- マルチタスクやマルチスレッド・システムにおいて、リソースの排他制御の問題などにより、プログラムの動作が事実上停止してしまう状態のこと
- 確保済みのリソースを強制的に解放したり、プログラムを強制終了させたりする必要がある

> 1. あるプログラム1と2があり、それぞれが共有リソースAとBを利用する
> 2. プログラム1はリソースAを確保後、リソースBを確保する
> 3. プログラム2はリソースBを確保後、リソースAを確保するものとする
> 4. この2つのプログラムを同時に起動すると、まずプログラム1はリソースAを確保し、プログラム2はリソースBを確保する(この時点でリソースAもBもそれぞれのプログラムから確保済みである)
> 5. プログラム1がリソースBを確保しようとするが、すでにプログラム2によって確保済みなので、それが解放されるまで待たされることになる
> 6. プログラム2はリソースAを確保しようとするが、すでにプログラム1によって確保済みなので、やはり待たされることになる
> 7. 結果、両方のプログラムがお互いに解放されることのないリソースを待ち続ける状態になる
> 
> これがデッドロック状態である。

https://atmarkit.itmedia.co.jp/icd/root/30/71009330.html

---

## CQRS
wip

---

## `nvidia-smi`コマンド
```bash
$ nvidia-smi

# smi -> System Management Interface
```
- NvidiaのGPUの使用率、温度、メモリ使用量など、GPUの詳細情報を取得するコマンド
- コマンド実行して情報が返ってくれば、GPUにアクセスできているということ
- GPUマシン上に構築したコンテナ内で実行すれば、コンテナがホストのGPUに正しくアクセスできているか確認できる

learn more… https://qiita.com/miyamotok0105/items/1b34e548f96ef7d40370

---

## GPU
<img width="740" alt="スクリーンショット 2023-10-04 21 54 55" src="https://github.com/naon708/til/assets/77439261/325537cc-9078-4c62-ab68-d7afb410e7ab">

https://www.d3.ntt-east.co.jp/00101/

- もともとは画像や動画、そしてゲームや3Dグラフィックスのレンダリングを高速に行うためのハードウェア
- 並列処理能力が高いため、機械学習の計算処理、特に大量のデータやパラメータを扱うディープラーニングのモデル学習においても使われるようになった

---

## Nvidia
- GPUを中心とした製品を開発・販売している企業
- 一般消費者向けGPU、データセンター向けGPU、機械学習用の高性能GPUを提供
- ハードウェアだけでなく、CUDAというGPU上での並列処理を容易にするプログラミングプラットフォームやDeepLearningのライブラリなどのソフトウェアも提供している

---

## 【Docker】コンテナ作成時の`gpus`オプション
```bash
docker container create --gpus=all --name use_gpu_project [Image]
```
- NVIDIAのGPUを搭載したマシン上にDockerコンテナを作成する際、`gpus`オプションを指定しないとホストマシンのGPUにアクセスできない
- GPUにアクセスしたい場合は指定が必要
- どのGPUを使うか指定する。値の指定がなければ、利用可能なGPU全てを使う
- `all`は利用可能なすべてのGPUを使う

https://docs.docker.jp/engine/reference/commandline/run.html?highlight=it#nvidia-gpu

learn more… https://qiita.com/ttsubo/items/c97173e1f04db3cbaeda

---

## 【GitHub】自分が担当したPRで、マージされたものがどれくらいあるか確認
```
is:pr is:merged assignee:@me
```

---

## LinuxでOSのバージョン確認
```bash
lsb_release -a
```

---

## LinuxでCPUアーキテクチャ確認
```bash
lscpu
```

---

## Zod (※上辺だけで全然理解してない)
- TypeScriptの検証ライブラリ
- TSは開発時の型安全性は担保されるけど、JSにコンパイルされてプラウザで動く時にTSの型情報は存在しないため、外部入力やAPIレスポンスなどの型安全は担保されていない
- 背景: GraphQLの型システムとTypeScriptの型システムは異なるため型情報と実態の不整合が起きていた

```ts
// 雰囲気だけのコード
import { z } from 'zod'

export const DateScalarSchema = z.string().brand<'DateScalar'>()
export type DateScalar = z.infer<typeof DateScalarSchema>
```
### 導入後
- asによる型キャストの頻度が減少
- 開発時にデータのスキーマ違反を検知できるようになった
- バリデーションによって詳細な型を利用できるようになった
- らしい
