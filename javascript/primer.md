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