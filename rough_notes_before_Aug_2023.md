## Linuxコマンドの`.`と`source`は同じ
```bash
. ~/.zshrc
source ~/.zshrc
```

---

## 【M1 Mac】下記の設定をしないとbrewコマンドが使えなかった
```zsh
# .zshrc
typeset -U path PATH
path=(
 /opt/homebrew/bin(N-/)
 /opt/homebrew/sbin(N-/)
 /usr/bin
 /usr/sbin
 /bin
 /sbin
 /usr/local/bin(N-/)
 /usr/local/sbin(N-/)
 /Library/Apple/usr/bin
)
```
https://zenn.dev/sprout2000/articles/bd1fac2f3f83bc
