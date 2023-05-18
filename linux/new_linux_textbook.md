## Linux環境のセットアップ
- VirtualBox7.0.8をインストール
  - https://www.virtualbox.org/wiki/Downloads
- ISOファイル: CDやDVDのディスクイメージをファイル化したもの
- CentOSのインストール
    - [https://www.centos.org/download/](https://www.centos.org/download/)
    - M1 MacはARMアーキテクチャなのでARM64を選択した
        - [https://ftp.yz.yamagata-u.ac.jp/pub/linux/centos-altarch/7.9.2009/isos/aarch64/](https://ftp.yz.yamagata-u.ac.jp/pub/linux/centos-altarch/7.9.2009/isos/aarch64/)

## M1 Mac × VirtualBoxで動かない

## Dockerで動かせた
```shell
docker pull centos:centos7

docker run -it -d --name centos7 centos:centos7

docker exec -it centos7 /bin/bash
```

## けどログインとかも試したいため、Intel Mac × VirtualBox環境で進める

## Tips
- スーパーユーザー: Linuxの管理者ユーザー
- Linuxは複数のユーザーが同時に利用することを前提としたシステム
- GUIの処理は、最終的に内部のCLIが動いてる
- ログアウトしてもLinuxは起動したまま
    - ひとりのユーザーが利用を終えただけ、と認識する
    - 誰もログインしていない状態でも、バックグラウンドではプログラムが動いている
- シャットダウン: OSを完全に停止させること

## rootユーザー

- `ユーザー名: root`でrootユーザーでログインできる
- rootユーザーになるとプロンプトが`$`から`#`に変わる
- シャットダウンができる

```bash
# -hは電源断
# now は今すぐシャットダウン(何分後にシャットダウンするか指定できる)
shutdown -h now

# 再起動
shutdown -r now
```

## シャットダウン

- Linuxでは基本シャットダウンしない
- ログアウトはするがシャットダウンしないで動かし続ける
- シャットダウンするのはメンテナンスや障害対応時のみ
- 現場の運用においてシャットダウンはとても重い操作

## シェルとLinuxカーネル

```bash
$ date
```

1. dateという文字列を受け取る
2. dateという名前のコマンドを探す
3. 見つかったコマンドを実行する　←カーネルが行う
4. 実行結果を画面に表示する

- カーネルはCPUやメモリ等のハードウェアの管理やプロセスの管理を行う

```bash
# /usr/bin/コマンドの場所
```

### シェルとLinuxカーネルが分かれている理由

- シェルが移植されていればLinux以外のOSでも同様に操作可能
- シェルがエラーや高負荷状態になってクラッシュしても、OS本体のLinuxカーネルへの影響を抑えられる

> 1つのプログラムには、1つのことをうまくやらせる。
> 
> 
> 1つのプログラムに機能を詰め込まず、機能ごとにプログラムを分離しておくことがソフトウェア開発において重要。
> 

## プロンプト

- prompt / 促す・鼓舞する / コマンド入力を待っている状態
- rootユーザーのときは`#`

### ログインシェル

- Linuxへのログイン時に最初に起動するシェルのこと
- デフォルトでは/bin/bash

```bash
# ログインシェルの確認
echo $SHELL
=> /bin/bash
```
### シェルの一時的な切り替え

```bash
# bourne shell(b-shell)に変更
$ sh
sh-4.2$

# bashに変更
$ bash
```

- shだと`ll`コマンドが使えなかった

## ターミナル

- 入出力のためのインターフェイス
- 昔は物理的なターミナルがあった
    - [https://en.wikipedia.org/wiki/Computer_terminal](https://en.wikipedia.org/wiki/Computer_terminal)

### ターミナルとシェルは別のソフトウェア

- MacからsshしてLinuxを操作する際、ターミナルはMacで、シェルはLinuxで動作する

## ソフトウェアライセンス

- GNU GPL (General Public License)
- Linuxのソースコードから派生物を作成して配布する場合は、ソースコードを公開しなければいけない

## ショートカットキー

```bash
# ヤンク
Ctrl + y
```

## 文字コード

```bash
man ascii
```

## ファイル

### Linuxはファイルでできている

- Linuxではユーザーデータもカーネルもシステム設定も、すべてがファイルとして表現されている

### システム全体でディスクが複数ある場合

- ディスクが複数あってもディレクトリツリーは1つ
- 2台目のディスクはルート配下のどこかにマウントする
- Windowsは2つのディレクトリツリーができるらしい

## Linuxディレクトリ構成の標準化仕様

- FHS (Filesystem Hierarchy Standard)
- [https://www.pathname.com/fhs/pub/fhs-2.3.html](https://www.pathname.com/fhs/pub/fhs-2.3.html)

| ディレクトリ名 | 英単語 | 内容 |
| --- | --- | --- |
| bin | binary | 実行ファイル |
| dev | devices | デバイスファイル (ハードウェアをファイルとして扱うため) |
| etc | etcetera | 設定ファイル |
| home | home | ホームディレクトリ (Linuxユーザーごとに割り当てられる個人用のディレクトリ) |
| sbin | system binaries | rootユーザー向けの実行ファイル |
| tmp | temporary | 一時的なファイル |
| usr | user / UNIX system resources | 各種アプリケーションとそれに付随するファイルを配置する (この中にはルートディレクトリ配下と似た構造を持つ) |
| var | variable | アプリケーションが動作した際に作られる可変のファイル |
| lib | libraries | システムやプログラムが動作するために必要な共有ライブラリやモジュール |
| src | source | 開発者がプログラムのソースコードを管理したりする |

### パス名展開

```bash
# *は任意の文字列
ls /bin/ba*

# ?は任意の1文字
ls /bin/ba??
```

### lsのコマンド引数は複数指定可能

```bash
ls /var /usr /bin
```

## コマンドオプション

```bash
# 30桁文の横幅で表示するオプション
ls -w 30

# ロングオプション
ls --width 30

# この書き方でもOK
ls --width=30
```

- `rails new –-database=postgresql` っぽい

```bash
# オプション確認
<command name> --help
```

### コマンドTips

```bash
# 複数指定でファイルを連結できる
cat /etc/crontab /etc/hostname
```

- `cat = concatenate`




