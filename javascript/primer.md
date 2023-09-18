## 関数の種類

```js
////// 関数宣言
function isTweetable(text) {
  return text.length <= 3;
}

////// Info: 関数名が無い関数を 「匿名関数」 or 「無名関数」 と呼ぶ

////// 関数式
const isTweetable = function(text) {
  return text.length <= 3;
}

////// アロー関数
const isTweetable = (text) => {
  return text.length <= 3;
}

////// アロー関数(省略形)
const isTweetable = text => text.length <= 3;


////// 関数コンストラクタ(覚えなくて良い)
const isTweetable = new Function('text', 'return text.length <= 3');

////// 出力
console.log(isTweetable('aaa'));
```

## コールバック関数
```js
//// 関数はなるべく分割して、責務をコンパクトにする(1つの関数にあれもこれもやらせない)
//// 引数で関数を受け取った関数が「高階関数」
//// 引数として渡されている関数が「コールバック関数」

// コールバック関数
const unfollow = function() {
  console.log("フォローを外しました");
}

// 高階関数
function confirmed(fn) {
  if (window.confirm("実行しますか？")) {
    fn();
  }
}

confirmed(unfollow);
```

## `window.confirm("Message")`
- 呼び出されるとブラウザの確認モーダルが開く(キャンセル or OK)
- OKを押すとtrueが返る
- キャンセルを押すとfalseが返る

## `window.prompt("Message")`
- テキストフォーム付きのモーダルが開く
- `入力した文字列 || null` が返る

## リポジトリ削除っぽい処理を関数を分割して書いてみた
```js
// フォーム付きモーダルを開いてユーザーからの入力を受け取る
const openModal = () => {
  const input_text = window.prompt("実行するには「ok」と入力してください")
  
  verification(input_text)
}

// 入力を検証
const verification = input_text => {
  input_text === "ok"　? deleteRepository() : console.log("Invalid input")
}

// 削除処理の想定
const deleteRepository = () => { console.log("Successfully deleted repository") }

// 削除ボタンを想定したモーダルを開く
window.confirm("Delete this repository") ? openModal() : console.log("Nothing happened")
```

## Web API 超入門
```js
///// async/await /////
/*
  メジャーな書き方
  非同期関数として定義
*/
async function callApi() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users");
  const users = await res.json();
  console.log(users);
}
callApi();


///// Promise chain /////
/* 若干古い */
function callApi() {
  fetch("https://jsonplaceholder.typicode.com/users")
    .then(response => response.json())
    .then(json => console.log(json))
}
callApi();


///// XMLHttpRequest /////
/* 記述が複雑な分、進捗がわかりやすい */
function callApi() {
  // インスタンスを生成
  const xhr = new XMLHttpRequest();
  // HTTPメソッドとURLを指定
  xhr.open("GET", "https://jsonplaceholder.typicode.com/users");
  // レスポンスのタイプ
  xhr.responseType = "json";
  // APIにリクエストを送る
  xhr.send();
  // 返り値の処理
  xhr.onload = function() {
    console.log(xhr.response)
  }
}
callApi();
```

## 関数の実行と参照の違い / `method` と `method()` の違い
```js
const resultA = methodA() 　　　 // 実行: methodAの実行結果を代入(関数の返り値)
const resultB = methodB　     // 参照: methodBそのものを代入(関数オブジェクト)
```
## addEventListenerには関数の実行結果ではなく関数オブジェクト(function)を渡す
- 下記のNGの方で書いており、意図した挙動にならなくて詰まった…
- addEventListenerの第二引数には実際には「呼び出し可能なオブジェクト」を渡す必要がある
```js
// NG
window.addEventListener("load", listUsers())

// OK
window.addEventListener("load", listUsers)
```

## ハンズオン (https://youtu.be/Oi38X7mNixE?si=prO7fgEtibQ3RHKp)
```js
/***** 自作 *****/

// Functions
async function fetchUsers() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users")
  const users = await res.json()
  return users
}

async function getUserNames() {
  const users = await fetchUsers()
  const userNames = []

  users.forEach(user => {
    userNames.push(user.name)
  });
  return userNames
}

async function initialDisplay() {
  const userNames = await getUserNames()

  userNames.forEach((name, index) => {
    const personId = document.getElementById(`person_${index += 1}`)
    personId.textContent = name
  });
}

// Main
initialDisplay()

// Click events
const button = document.getElementById("button")
button.addEventListener("click", async function() {
  const okawariUserNames = await getUserNames()
  console.log(okawariUserNames)
  const ol = document.getElementById("nameList")

  okawariUserNames.forEach((name) => {
    console.log(name)
    const li = document.createElement("li")
    li.appendChild(document.createTextNode(name))
    ol.appendChild(li)
  })
})


/***** リファクタ後 *****/

// DOM
const ol = document.getElementById("ol")
const button = document.getElementById("button")

// Functions
const createList = (user) => {
  const li = document.createElement("li")
  li.innerText = user.name
  ol.appendChild(li)
}

const fetchUsers = async () => {
  const res = await fetch("https://jsonplaceholder.typicode.com/users")
  const users = await res.json()
  return users
}

const listUsers = async () => {
  const users = await fetchUsers()
  users.forEach(createList)
}

// Events
window.addEventListener("load", listUsers)
button.addEventListener("click", listUsers)
```


## ECMAScript と CommonJS
※ そもそも概念が異なるものなので比較するのは微妙なところ(比べるとしたら`ESModules`と`CommonJS`)

### ECMAScript
- JavaScriptの中核となる言語仕様
- ブラウザ上で動くJavaScript(クライアントサイドJavaScript)には、Node.jsにはないメソッド(e.g. `window.alert()`, `document.getElementById()`)があるし、逆も然り
- どの実行環境でも動作する共通の仕様

### CommonJS
- JavaScriptのモジュールシステムの一つ
  - JavaScriptのコードを複数のファイルに分割し、その中で特定の変数や関数を別のファイルから使い回したり、特定のスコープ内で隠蔽したりする仕組みのこと
  - e.g. `CommonJS`, `ECMAScript 20** Modules`, `AMD`

> 2009年ごろ、JavaScriptでサーバサイドも作りたいという人が現れたが、JavaScriptはブラウザ上で動かすために生まれた言語のため、 「モジュール定義や読み込みもない、標準入出力もない、File I/Oもない、標準的に欲しいものが色々ない。」 という状況の中で、Node.jsのようなサーバサイドでJavaScriptが動く環境が多く生まれた。「それぞれで、勝手にAPIを作るのではなく、標準的なAPIの仕様を決めて、それに沿った実装にしよう、そうすれば、色んなサーバサイドJavaScript環境で動くだろう。」と言って始まったのがCommonJS

ref. https://hackmd.io/@iFxVSfmNQBi89A13TWdmpw/HJYvmQLQq

### CommonJS と ECMAScript の違い
- CommonJSで書かれたJSは、ChromeやSafariなどのブラウザで動かない。逆にECMAScriptでは動く。
- ECMAScriptがモジュールシステム(ESM)以外の文法も定めている規格なのに対して、CommonJSはブラウザ外でのモジュールシステムに焦点を当てている規格。
- ただCommonJSは、機能というよりかは「サーバー側JavaScriptの仕様を統一しよう」というプロジェクトと捉えた方が良さそうで、Node.jsが独自の進化を遂げCommonJSのPJは動いていない状態。

```js
// CommonJS: ChromeやSafariなどのブラウザで動かない、主ににNode.jsなどのサーバサイドで使用される。

require　＝　CommonJS
```
```js
// ECMAScript: ChromeやSafariなどのブラウザで動く、主にクライアントサイド(Webブラウザ)で使用される

import　＝　ECMAScript
```


## パッケージの依存関係を可視化してくれるサイト
- https://npm.anvaka.com/

## ランタイムとは？
- ブラウザで実際に動くタイミングのこと
- モジュールバンドラー登場以降は、開発時に書いてるコードとランタイムのコードが異なるためそれを表現するために使う用語

## コードを事前にブラウザ用に変換
することによって、モジュールの依存関係を解決できたり、新機能を使えるようになった。

- Bundle
  - 主な目的: モジュールの依存関係を解決
  - ブラウザで読み込める形に変換してくれる
  - webpackの登場でjs以外のファイル(cjs, scss, png)もバンドルできるようになった
- Compile
  - 主な目的: 開発者体験を向上
  - ブラウザで読み込める形に変換してくれる
  - Babelの登場で新ESで書いたコード(const, アロー関数とか)を古い仕様のブラウザでも読み込めるように変換できるようになった
  - コンパイルが当たり前になったことで、Vue、React、TypeScriptとかが流行りだす

## JavaScriptを取り巻く3つの概念
- モジュール(モジュールシステム) / 名詞
  - コードを適切なサイズに分割したファイル
  - 1ファイル1モジュール
  - 名前空間を解決する
  - e.g. `CommonJS`, `ES Modules`, `AMD`
- パッケージ管理システム / 名詞
  - パッケージ: コードを再利用可能な状態にしたもの
  - 公開されているパッケージ群の依存関係を解決しつつ管理するもの
  - e.g. `npm`, `yarn`, `pnpm`
- ビルド / 動詞
  - JavaScriptコードを異なる環境で動かすための事前準備
  - Bundle & Compile
  - e.g. `Webpack`, `Vite`, `Rollup`


## Named export/import
- namedのときは`{}`で囲う
```js
/***** index.js *****/

// imports
import { name, name2, displayLog, log } from "./user.js"

// using
document.body.textContent = name + name2
displayLog(name)
log(name2)

/***** user.js *****/

// variables
const name = "ピカ"
const name2 = "チュウ"

// functions
function displayLog(value) {
  console.log(value)
}
const log = value => console.log(value)

// exports
export { name, name2, displayLog, log }
```
### エイリアスを付けられる
- named export/import すると名前が被ることがある
  > Identifier 'name' has already been declared
- importする側で使うことが多い
  ```js
  import { name, logger } from "./user.js"
  import { name as anotherName } from "./user2.js"
  ```
### 宣言とexportを同時にしてもよい
```js
export const name = "ピカ"
export const log = value => console.log(value)
```

## Default export/import
- 前提、そこまで使う機会ない。フレームワークのお作法として使われてるくらい(?)
- defaultのときはむき出し
  ```js
  export default name
  /* ~~~~~~~~~~~~~~~~~~~~~~~~~~ */
  import name from "./user.js"
  ```
- 使えるのは1モジュールにつき1回のみ

### `named export`だと変数自体を渡すが、`default export`では変数を評価した値を渡す
```js
// user.js
const name = "ほげ"
export default name
```
```js
// このnameには変数を評価した値「ほげ」が直接渡されている
import name from "./user.js"

//　なのでexport defaultで指定した変数名以外の名前でimportしてもOK
import anotherName from "./user.js"
```

### `default export`の場合は変数は宣言とexportを同時に行えない
```js
// NG
export default const name = "ピカ"

// OK(関数はOK, 関数式はダメっぽい)
export default function log(value) {
  console.log(value)
}

// OK(関数名無くてもOK)
export default function(value) {
  console.log(value)
}
```

## Namespace import
```js
// someModule.js
const someVariable = "something"
const someFunction = value => console.log(value)

export { someVariable, someFunction }
```
```js
// someModule.jsモジュールでexportされたすべてのメンバーがallMembersオブジェクトに集約される
import * as allMembers from "./someModule.js"

allMembers.someVariable
allMembers.someFunction
```

## Re-exporting
- `親モジュール - 子モジュール - 孫モジュール` の関係で、孫モジュールがexportしたものを子モジュールで再度exportできる
  - importする側が親、importされる側が子
- この機能のおかげで、様々なモジュールからの様々なエクスポートを集約した1つのモジュールを作成することができる

```js
// parent.js
import * as someModule from "child.js"

console.log(someModule.foo)

// child.js
export * from "grandchild.js"

// grandchild.js
const foo = "foo"
const bar = "bar"
const baz = "baz"

export { foo, bar, baz }
```
- Re-exportだけで７種類くらい書き方あるけど読めばわかるから記憶しなくてよさそう
- `default`は予約語のため、`import { default } from "base.js"`みたいな書き方はできない

### ライブラリってこんな感じかな
- `user-actions`というライブラリがあるとする
```js
// index.js
import * as userAction from "./user-actions/index"

console.log(userActions.like, userActions.subscribe, userActions.notify)

// user-actions/index.js
// 複数モジュールを集約
export * from "./like"
export * from "./subscribe"
export * from "./notify"

// user-actions/like.js
// user-actions/subscribe.js
// user-actions/notify.js
```

## Side effect import
- side-effect: 副作用
- 特定の値をimportせずに、子モジュール内のすべてのトップレベルのコードを実行する

### トップレベルのコード
```js
/***** main.js *****/
import "./side-effect.js";

/***** side-effect.js *****/
// これはトップレベルのコードです
const a = 10; // 親モジュールでアクセスできない
console.log("I am a top-level code.");　// 親モジュールで実行された

// これはトップレベルのコードです
function myFunction() {
  // これはトップレベルではありません
  console.log("I am inside a function.");　// 親モジュールで実行された
}

// これはトップレベルのコードです
class MyClass {
  constructor() {
    // これはトップレベルではありません
    this.myValue = 20;　// 親モジュールでアクセスできなかった
  }

  myMethod() {
    // これはトップレベルではありません
    console.log("I am inside a method.");　// 親モジュールで実行されなかった(このモジュール内で生成されたインスタンスにアクセスできないため)
  }
}

// これはトップレベルのコードです
myFunction(); // 親モジュールで呼び出せない

// これはトップレベルのコードです
const myInstance = new MyClass(); // 親モジュールでアクセスできなかった(インスタンスにもクラスにもアクセスできない)
```

### Polyfill
> ポリフィルとは、最近の機能をサポートしていない古いブラウザーで、その機能を使えるようにするためのコードです。
> 
> 現代のブラウザーは、ほとんどが標準的な意味に従って幅広い API を実装しているので、ブラウザー固有の実装を処理するためにポリフィルを使うことは今日ではあまり一般的ではなくなりました。

### まとめ
- 副作用モジュールのトップレベルのコードは実行はされるけどアクセスできない
- 各種ブラウザ対応(Polyfill)やログを送る等の処理を噛ませたいときに使うらしい


## パッケージ管理ツール
- そもそもライブラリをダウンロードする方法は主に3つ
  - パッケージ管理ツール or CDN or ローカルにサイトからダウンロード
 
```shell
yarn add jquery
```
- package.json, yarn.lockが更新され、node_modulesディレクトリにjQueryが追加される
- dist → distribution(配布)

### CDNと比較
```html
  <!------- jQuery読み込み ------->
  <!-- Yarn -->
  <script src="node_modules/jquery/dist/jquery.min.js"></script>
  <!-- Google CDN -->
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>
```
- CDNの方はURLにバージョンが記載されているが、yarnの場合は`package.json`, `yarn.lock`で管理されている
  ```json
  "dependencies": {
    "jquery": "^3.7.1"
  }
  ```

### インストール済みパッケージのバージョン変更
  - package.jsonのバージョンを書き換えて`yarn`コマンド実行
  - `yarn upgrade jquery --latest`コマンドとかでも可能
  - `yarn add jquery@3.7.1`のようにバージョンを直接指定して追加すれば勝手に更新してくれる
  

## CDN / Content Delivery Network
> ウェブ上でコンテンツ（画像、ビデオ、スタイルシート、JavaScriptファイルなど）を効率的に配信するためのネットワークインフラストラクチャです。
>
> CDNは世界中に分散されたサーバー群からなり、ユーザーに最も近い地理的位置にあるサーバーからコンテンツを配信します。

### jQueryを使用したサンプルコード
```html
<body>
  <h1>テスト</h1>

  <!-- Google CDN -->
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>

  <!-- jQuery CDN -->
  <script src="https://code.jquery.com/jquery-3.7.1.js" integrity="sha256-eKhayi8LEQwp4NKxN+CfCh+3qOVUtJn3QNZ0TciWLP4=" crossorigin="anonymous"></script>

  <!-- CDNJS CDN -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>

  <script>
    // jQueryの記述
    $('h1').css("color", "green");
  </script>
</body>
```
- 利点
  - 地理的に分散されているため高速
  - ブラウザにダウンロードされたファイルはキャッシュされるため高速かつサーバーの負荷も少ない
  - ライブラリが共通して使われていれば、キャッシュが効くので他のWebサイトの読み込みも早くなる
- 注意点
  - CDNベンダーのサーバーがダウンするとWebページが壊れる可能性がある
  - 信頼できないCDNから読み込むとセキュリティリスクが発生する

### 各ベンダーがCDNを提供する意図としてはこんなものがある
- 広告とブランド: 無料でCDNを提供することでその企業やサービスに対する認知度が上がる
- 市場調査や製品開発: どんなコンテンツがどれほどの頻度でアクセスされているか等のデータを収集できる
- コミュニティ: OSSとして技術コミュニティへの貢献


## `*.css.map`ファイル
- cssファイルとscssファイルをマッピングしてデバッグし易くするもの
- mapファイルがあることで、ブラウザのDevToolsで確認できるcssファイルから、scssのファイルや行数を特定できる

### `path/to/sass style.scss:style.css`実行
- cssファイルとmapファイルが生成された

```scss
// style.scss
div {
  background-color: aqua;

  h1 {
    color: coral;
  }
}
```
```css
/* style.css */
div {
  background-color: aqua;
}
div h1 {
  color: coral;
}

/*# sourceMappingURL=style.css.map */
```
```js
// style.css.map
{"version":3,"sourceRoot":"","sources":["style.scss"],"names":[],"mappings":"AAAA;EACE;;AAEA;EACE","file":"style.css"}
```

## グローバルインストール
```bash
yarn global add sass
```
- `which sass`したらsass not foundと言われた
  ```bash
  # シンボリックリンク
  lrwxr-xr-x  1 user01  staff  48  9 16 22:55 /Users/username/.yarn/bin/sass@ -> ../../.config/yarn/global/node_modules/.bin/sass
  ```
- sassコマンドを使うにはパスを通す必要がありそう

### 実行 (scssをcssにビルドするコマンド)
- `~/.yarn/bin/sass style.scss:style.css`
- パスを通せば`sass style.scss:style.css`でいけると思う

### グローバルなディレクトリのパス
```bash
# globalでインストールしたパッケージはここに格納されるっぽい
ls /Users/user_name/.config/yarn/global/
node_modules/ package.json  yarn.lock
```
- グローバルなディレクトリの場所を確認する
  ```shell
  # yarn
  yarn global dir
  
  # npm
  npm root -g
  ```

## ローカルインストール
```shell
yarn add sass
```

```shell
./node_modules/.bin/sass style.scss:style.css
```
### チーム開発時
- グローバルにインストールしたnode_modulesだとメンバー間でバージョンの乖離が起き得る
- 共有されている`yarn.lock`等を元にローカルにインストールしたnode_modulesを参照することで、チームでバージョンを固定できる


## npm script
- `./node_modules/.bin/sass style.scss:style.css`のようなスクリプトを、簡潔な書き方で定義できる
```json
"scripts": {
  "build:css": "sass",
  "hoge": "echo ほげ",
  "date": "date"
},
```
※ 値を`sass`とするだけで勝手にnode_modules/.bin配下を見てくれる

```shell
yarn build:css sample.scss sample.css

# npmコマンドでも動く
npm run build:css sample.scss sample.css
```

## npx (パッケージランナーツール)
### 背景
- `create-react-app`のような頻繁に使わない(セットアップ用等)のコマンドは、容量節約したいためローカルに落としたくない

### 機能
- パッケージをグローバルにもローカルにもインストールせずに一時的にダウンロードして実行できる

### 補足
- yarnでは`yarn dlx`という`npx`に相当するものがあるがあまり使われていないので、普段yarnを使っていても一度限りしか使わないコマンドはnpxで行うのがスタンダード
- npxコマンドを使う際はタイポに注意(本家に寄せた名前でマルウェアの可能性あり)

## serve
- https://github.com/vercel/serve
  - npxを利用することで`npx serve`でローカルサーバーを立ち上げられる


## Babel
- ESバージョン解決以外にもTypeScriptやJSXもBabelで変換できる
- Babelを試す
  - https://babeljs.io/repl

## Webpack
- モジュールバンドラー
- ひとことで表すと「複数モジュール(ファイル)をひとまとめにしてくれるツール」
- モジュール同士の依存関係を解決し、ブラウザで動くようにしてくれる
  - sassとかcjs等そのままではブラウザで動かないファイルを変換する
  - 画像の最適化
  - ファイルの圧縮
  - etc...

<img src="https://github.com/naon708/til/assets/77439261/a06a47b5-0119-4c63-8fcc-cca0bc89e177" alt="webpack-image" width="80%" />

## `yarn webpack`
- ビルドコマンド

```shell
yarn webpack --mode=development

asset bundle.js 4.06 KiB [emitted] (name: main)
runtime modules 670 bytes 3 modules
cacheable modules 89 bytes
  ./src/index.js 35 bytes [built] [code generated]
  ./src/bar.js 54 bytes [built] [code generated]
webpack 5.88.2 compiled successfully in 77 ms
✨  Done in 3.03s.
```

## 開発用と本番用のバンドルファイル(ビルド後のファイル)の違い
```js
// --mode=development

/******/ (() => { // webpackBootstrap
/******/ 	"use strict";
/******/ 	var __webpack_modules__ = ({

/***/ "./src/bar.js":
/*!********************!*\
  !*** ./src/bar.js ***!
  \********************/
/***/ ((__unused_webpack_module, __webpack_exports__, __webpack_require__) => {

// 続く...
```
- ブラウザでのデバッグに必要な情報等も記載されている状態
```js
// --mode=production

(()=>{"use strict";console.log("bar")})();
```
- 余計な記述を排除して完全に最適化されている状態
