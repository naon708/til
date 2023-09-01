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
