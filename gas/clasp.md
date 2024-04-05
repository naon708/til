## 参考記事
- hoge

## いきなり余談
- `-g` オプションで npm インストールした際、なぜ　node_modules が衝突しないのか？
- グローバルな node_modules 配下にパッケージごとにディレクトリが切られていて、その中で それぞれのパッケージに必要な node_modules が格納されているため。
- その前段階で node のバージョンごとにディレクトリを切ったりしているので node の異なるバージョン間で衝突しないようになっているのがわかった。
```
# node 12.22.10
ls ~/.anyenv/envs/nodenv/versions/12.22.10/lib/node_modules/                                                                                                               ✔
@google/    npm/        typescript/ yarn/

# node 18.19.0
ls ~/.anyenv/envs/nodenv/versions/18.19.0/lib/node_modules/                                                 ✔
corepack/ npm/      yarn/
```
