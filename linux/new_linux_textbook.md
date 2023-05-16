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

