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

### common.js と ECMAScript の違い
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


## named export/import
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

## default export/import
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
