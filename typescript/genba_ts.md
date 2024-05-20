現場で使えるTypeScript<br>
https://book.mynavi.jp/ec/products/detail/id=142709

## memo

```ts
// インデックスシグネチャの使い方
interface SomeObj {
  // 型変数の部分は慣例で K か key など
  [key: string]: string | number;
  [key: number]: number | string;
  [key: `project_${number}`]: number; // テンプレート文字列
}

const hoge: SomeObj = {
  foo: 'foo',
  bar: 2,
  3: 'baz',
  project_1: 99
}
```
- プロパティの値が決まっているものは、インデックスシグネチャを使わずに具体的に定義した方が良いと思う
- 入る値が確定していないプロパティ（外部APIのレスポンスなど）に適しているらしい

---
- 関数オーバーロードはあまり使わないほうが良い気が…（可読性が低いし、代替案結構ありそう）
---

```ts
// void
const greet = (name: string): void => {
  console.log(`hello, ${name}`)
}

greet('alice')

// never
const throwError = (): never => {
  throw new Error('This is error text !!!!!!!')
}

throwError()
```
- void は、呼び出し元に何も返さない
  - が、関数は正常に終了する
- never は、呼び出し元に制御を返さない
  - 具体的には、無限ループや例外による異常終了のようなパターン

---

```ts
// 関数型の型エイリアスを定義
type AddFunction = (a: number, b: number) => number

const someFunc: AddFunction = (a, b) => a + b
```
- 関数型
  - 関数も引数と返り値の型をチェックできる

---

```ts
const sumFunc = (a: number, b: number) => {
  return a + b
}
```
- パラメーターは型推論できない。
- 関数の返り値は型推論されるので必ずしも書く必要はないが、ドキュメントとしての役割(返り値の型がわかりやすい)や開発効率(意図しない型を return していることにすぐ気付ける)のために明示するのも◯

---

- Unknown Type
  - どんな値も入れられるが、unknown型として定義された変数は型チェックされる

  ```ts
  // 整数を代入したとしても
  const unknownValue: unknown = 33
  
  // number型として定義できないし、
  const numberValue: number = unknownValue
  
  // 算術演算もできない
  unknownValue + 3
  ```

- unknown は「未知の値」。undefined は「未定義の値」。null は「空」。
- undefined と null は値を設定できないときに一時的に入れる値。
- null はオプショナルチェーン演算子(`?.`)と合わせて使うことが多そう(?)


---

```ts
// Array型
type arrayType = (number | boolean)[]

// Tuple型
type tupleType = [number, boolean]
```
- tuple型 は順序と要素数を指定する
- が、オプショナルやスプレッド構文を使うことで柔軟に扱える

---

- Union Type
  - A または B
  - `string | number`
    - string もしくは number の型を許容する
- Intersection Type
  - A かつ B
  - `object1 & object2`
    - object1 と object2 のプロパティを持つ（過不足は許容しない）
