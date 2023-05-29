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

