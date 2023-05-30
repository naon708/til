## エイリアス

```bash
alias ls='ls -F'
type ls
#=> ls is aliased to 'ls -F'

unalias ls
```

### エイリアスを無効化して実行する方法

```bash
/bin/ls

command ls

\ls
```

## bashオプション

```bash
set -o ignoreeof
set +o ignoreeof
```

```bash
shopt -s cdspell
shopt -u cdspell
```

## シェル変数

```bash
# スペースは付けない(スペースを付けるとコマンドとして認識されてしまう)
var='test variables'
echo $var
```

### プロンプトのカスタマイズ

```bash
# 「Prompt String 1」の略称でプロンプト文字列を表す環境変数
PS1='[\u@\h \w] \$'
```

### パスを通す

```bash
# ~/binというディレクトリを検索パスの最後に追加する
PATH="$PATH:~/bin"
```

- PATHにはディストリビューションごとに適切な値があらかじめ設定されている
- インストールしたアプリケーションや自作ディレクトリを検索パスに含めたい場合に行う

### 定番のシェル変数

```bash
PATH LANG HOME PWD SHELL
```

## バックスラッシュがうまく打てなかった

- `option + ¥`だと打てなくて、`¥`のみ入力で打てた

## 環境変数

### 前提

- コマンドには外部コマンドと組み込みコマンドがある
- 外部コマンド：ファイルシステム上に実行ファイルとして存在するコマンド(`/bin/cat`など)
- 組み込みコマンド：シェルに内蔵されているコマンド(`echo`など)
- typeコマンドで確認できる

### シェル変数の参照

- 外部コマンドはシェル変数を参照できない
- 環境変数を設定することで外部コマンドから参照できるようになる

```bash
LANG=en_US.UTF-8
cat --help
#=> 英語で表示される
```

- catコマンド実行→環境変数の値をチェック→その設定に従って動作

```bash
# 環境変数一覧表示
printenv
```

```bash
# シェル変数LESSを環境変数として設定する例
export LESS='--no-init'
```

## bashの設定ファイル

```bash
# ログインシェルで起動したときに読み込まれる順番
/etc/profile --> ~/.bash_profile --> ~/.bashrc

# 非ログインシェルの場合
~/.bashrc
```

- `/etc/profile` システム全体
- `~/.bash_profile` ユーザー個別 & ログイン時のみ読み込まれる
- `~/.bashrc` ユーザー個別 & bashを起動するたびに読み込まれる

### シェルのカスタマイズ

- 基本的に`~/.bashrc`に書けば良い



