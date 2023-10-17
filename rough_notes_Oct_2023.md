## `ps aux`の出力がウィンドウから途切れる時
```bash
ps aux | less
```

---

## 【機械学習】モデルの評価と推論の違い
- 「モデルの評価」はモデルの性能を測定するためのプロセスであり、「推論」はトレーニング済みのモデルを使用して新しいデータに対する予測を行うプロセス

---

## 【セキュリティ】SIMスワップ
```plain
攻撃者が個人情報を入手 → 身分証明書を偽造 → 偽の身分証明書でSIMカードを再発行 → 再発行したSIMカードで2段階認証を突破
```
- 2段階認証も突破する
- 漏れた個人情報は闇サイトで売買される

https://xtech.nikkei.com/atcl/nxt/column/18/02574/090700009/?i_cid=nbpnxt_ranking

---

## ERP (Enterprise Resource Planning)
- 企業の経営資源を有効に活用し、経営の効率化を目的とする手法
  - 経営資源: `ヒト・モノ・カネ・情報`
- `業務システム`：基幹システムと情報システムを合わせたシステムの総称。
  - `基幹システム`：企業の経営上必要不可欠なシステム
  - `情報システム`：業務を円滑に遂行するためのシステム
- クラウド化のトレンドにより、ERPをクラウド上に構築できるサービスが増加している
  -  CRM: HubSpot, SalesForce
  -  会計: freee, MoneyForword
  -  コミュニケーションツール: Slack, Chatwork
  -  ナレッジベース: Kibela, Notion

<img width="540" alt="about_cafis" src="https://github.com/naon708/til/assets/77439261/ca320022-33c5-4421-8fd1-bc2226b44cf2">

### CRM(Customer Relation Management)
- 顧客関係管理
- 新規顧客の獲得が難しくなっている昨今、既存顧客との関係性向上(ナーチャリング)が重要で、それに必要な情報を管理すること

---

## CAFIS (Credit And Finance Information System)
- クレジットカード決済において、加盟店とカード発行会社（または金融機関）の間の通信を仲介する役割を持つシステム(1984年サービス開始)
- 決済端末そのものではない
- `クレジットカード`から提供開始し、`電子マネー`や`QRコード決済`、`インバウンド決済`などにも対応
  - `インバウンド決済`: 海外のキャッシュレス決済サービス(AliPayなど)

<img width="600" alt="about_cafis" src="https://github.com/naon708/til/assets/77439261/892d31f5-c2a6-488b-a3ad-ce91559548ac">

https://www.nttdata.com/jp/ja/lineup/cafis/

---

## メインフレーム
- 昔主流だった、基盤システム等に用いられる集中管理型の大型コンピュータシステム
- メインフレームを使用して構築したシステムは、現在のクライアント/サーバシステムやクラウドなどのWebシステムと比較するとレガシーシステムではある

<img width="453" alt="スクリーンショット 2023-10-14 20 11 25" src="https://github.com/naon708/til/assets/77439261/8109ff92-3db9-4698-9a87-abbb3c6d1264">

[ref](https://chiba-it-literacy.jimdofree.com/it%E7%94%A8%E8%AA%9E%E8%A7%A3%E8%AA%AC/it%E7%94%A8%E8%AA%9E%E5%9F%BA%E6%9C%AC%E7%B7%A8/%E3%83%A1%E3%82%A4%E3%83%B3%E3%83%95%E3%83%AC%E3%83%BC%E3%83%A0%E3%81%A8%E3%82%AF%E3%83%A9%E3%82%B5%E3%83%90/)

### ようするに
- クラサバ以前に主流だったデカいコンピューター
- サーバーといえばサーバー

---

## 全銀システムで他行振り込みができない障害発生
- 銀行間送金を行う「全国銀行データ通信システム（全銀システム）」で不具合が発生。
- 約6年おきに行われる中継コンピューター（RC）のシステム更改時に発生。
- 全銀システムには、平日の取引を処理する`コアタイムシステム`と、夜間や休日の取引を処理する`モアタイムシステム`が存在。
  - 2023年10月10日午前8時30分ごろにコアタイムシステムに切り替わった際に障害が発生。
  - 送金の約140万件のうち、約40万件が処理されず、後日対応が必要となった。
- 一部の金融機関で不具合が発生しなかった理由は、設定の方法が異なっていたため。

https://xtech.nikkei.com/atcl/nxt/news/18/16068/?i_cid=nbpnxt_sied_blogcard

### この障害が発生する半年前に技術アップデートの実施が発表されていた
- 新しい動作プラットフォームは、既存の富士通製メインフレームからの移行を予定し、オープン基盤を採用する方針。
- 既存の`COBOL`プログラムは`Java`などに書き換えることを検討中。
- 次期システムでも`コアタイムシステム`と`モアタイムシステム`の2つのシステムで取引を処理する構成は維持される予定。

[次期全銀システムは富士通メインフレームとCOBOLから脱却へ、何が変わるのか](https://xtech.nikkei.com/atcl/nxt/column/18/00001/07669/)

### まとめると
COBOLとかメインフレームといった古い技術基盤を使い続けていることが原因で発生した。これらの技術を扱える技術者も減っていくし、システムに造詣のある人間が金融庁におらず「とにかく安全」に固執している金融機関の企業体質が問題の本質のよう。

### 一連のニュース
[50年間で初めての大規模障害、全銀システムが分かる厳選記事](https://xtech.nikkei.com/atcl/nxt/info/18/00037/101100141/?i_cid=nbpnxt_sied_blogcard)

---

## canvasの座標は左上が原点
<img width="363" alt="スクリーンショット 2023-10-12 21 44 13" src="https://github.com/naon708/til/assets/77439261/371d6293-1e8c-4f56-b332-4e7866819238"><br>
https://www.yugien.xyz/web/api/canvas/coordinate_system.html

```js
function draw_triangle() {
  var canvas = document.getElementById("canvas");
  if (canvas.getContext) {
    var ctx = canvas.getContext("2d");

    ctx.beginPath();
    ctx.moveTo(150, 0);
    ctx.lineTo(0, 150);
    ctx.lineTo(300, 150);
    ctx.fill();
  }
}
```



---

## 【機械学習】Early Stoppingの考え方

<img width="821" alt="スクリーンショット 2023-10-12 21 32 07" src="https://github.com/naon708/til/assets/77439261/2c6ae4b3-33be-480b-95b3-602bffe339f2"></br>
https://zero2one.jp/ai-word/early-stopping/

- トレーニングの途中でも、モデルの性能が頭打ちになったり過学習となる場合は早期に終了することがある
  - 過学習の防止
    - 過学習: モデルがトレーニングデータに過度に適合してしまい、検証データや未見のデータでの性能が低下すること
  - トレーニング時間やリソースの節約
- 判断基準
  - トレーニングの途中で評価を行いながら、モデルの性能が一定のepoch数にわたって向上されない場合は止める

---

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
