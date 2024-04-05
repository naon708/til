## 参考記事
- https://developers.google.com/apps-script/guides/clasp?hl=ja
- https://codelabs.developers.google.com/codelabs/clasp#0
- https://github.com/google/clasp

## コマンドログ
```shell
node -v
=> v12.22.10

npm install @google/clasp -g

clasp
clasp login
=> Default credentials saved to: ~/.clasprc.json

https://script.google.com/home/usersettings にアクセスしてAPI設定をONにした

clasp create first_clasp_app # 新規プロジェクト作成
clasp clone [scriptId] # 既存プロジェクトをクローン
clasp pull # カレントディレクトリに対応したプロジェクトの最新の状態を取り込む
clasp push # カレントディレクトリに対応したプロジェクトに最新の状態をプッシュする
clasp version
clasp deploy
clasp undeploy [deploymentId]
clasp versions
clasp deployments
clasp status # リモート ↔ ローカル 間で共有されているファイルと　ignore されているファイル一覧
clasp list # プロジェクト一覧
```

## いきなり余談
- `-g` オプションで npm インストールした際、なぜ　node_modules が衝突しないのか？
- グローバルな node_modules 配下にパッケージごとにディレクトリが切られていて、その中で それぞれのパッケージに必要な node_modules が格納されているため。
- その前段階で node のバージョンごとにディレクトリを切ったりしているので node の異なるバージョン間で衝突しないようになっているのがわかった。

```shell
# node 12.22.10
ls ~/.anyenv/envs/nodenv/versions/12.22.10/lib/node_modules/                                                                                                               ✔
@google/    npm/        typescript/ yarn/

# node 18.19.0
ls ~/.anyenv/envs/nodenv/versions/18.19.0/lib/node_modules/                                                 ✔
corepack/ npm/      yarn/
```
