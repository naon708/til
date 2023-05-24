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

```bash
cp file1 file2 file3 dir1
# directoryをコピーする場合はrオプションで再帰的にコピー
cp -r dir1 dir2
```

```bash
mv file1 file2 file3 dir1
# mvはrオプションいらない
mv dir1 dir2
```

## リンクを張る

- ファイルに別名をつけること
- ハードリンク
    
    ```bash
    ln file1 file2
    ```
    
    - ハードリンクで紐づけたファイルはすべて本物(実体)
    - 紐づけたファイルすべて削除すると完全削除となる
- シンボリックリンク
    - ファイルの実体は1つ
    
      ```bash
      ln -s file1 file2

      ls -F
      file1 file2@

      ls -l
      file2 -> file1
      ```

      ```bash
      # シンボリックリンクの実体ファイルを削除
      rm file1
      ```
    
    - リンクが切れる
    
      <img width="944" alt="スクリーンショット 2023-05-21 23 43 38" src="https://github.com/naon708/til/assets/77439261/a85f0700-c4b0-4788-8489-318166f4b0f6">

    

### シンボリックリンクの使い道

- 深いパスにエイリアスを付けて呼び出しやすくする
- 複数バージョンのファイルが存在するとして、最新バージョンにlatestみたいなエイリアスを付けたりする

  ```bash
  # 既に存在するエイリアスを貼りなおす
  ln -nfs ver1.0.9 latest

  # リンク取り消し
  unlink file1
  ```

## ファイルを探す

### find

- 実行時にディレクトリツリーを下ってファイルを検索する(ファイル数が多いと時間がかかる)

```bash
# findでワイルドカードを使う場合はクォートで囲む
find . -name '*.txt'
```

- クォートで囲んだ文字列ではbashのパス名展開が行われない

```bash
# 条件のAND検索
find . -type f -name '*.txt'
```

### locate

- 事前に作られたファイルパスのデータベースを検索する(速い)

```bash
# locateコマンドを含むパッケージをインストール
yum install mlocate

# 検索用データベース作成
updatedb
```

```bash
# 大文字小文字を区別しない
locate -i notes

# ファイル名のみ
locate -b python
```

```bash
# OR検索
locate bash doc

# AND検索
locate -A bash doc
```

## コマンドの使い方を調べる

```bash
cat --help
man cat # 詳しく
info cat # さらに詳しく
```

### 説明文の文字列で検索

```bash
man -k cron
```

### マニュアルのセクション

```bash
# セクション番号を確認
man -wa crontab

# マニュアルのどのセクションを見たいか指定
man 1 crontab
man 5 crontab
```

## コマンドの場所

- Linuxではコマンドもファイルとして存在している
- シェルがコマンド実行時に$PATHで設定された場所からコマンドを探してくれる

```bash
echo $PATH
=> /usr/local/bin:/usr/bin:/usr/......   # これを サーチパス/パス と呼ぶ
```

### コマンド実行時、シェルがどのファイルを実行するのか確認する

```bash
which cat
=> /bin/cat

# すべての実行ファイルのパスを表示
which -a cat
```

## コマンドのドキュメント

- 日本語doc
    - [http://linuxjm.osdn.jp/](http://linuxjm.osdn.jp/)

```bash
# 日本語に設定
LANG=ja_JP.UTF-8

# デフォルト(英語)に設定
LANG=C
```

## ファイルの種類

- テキストファイル
    - 文字列が書かれたファイル
        - プログラムのソースコードなど
- バイナリファイル
    - 画像ファイル
    - 音声ファイル
    - Linuxの実行ファイル(`/bin/cat`など)

## Vim

- 実質的なLinuxの標準エディタ

```bash
# 現在のカーソル下の単語をコピー
b -> yw

# 現在の行とすぐ下の行を1行に連結
J
```



