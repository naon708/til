現場で使えるTypeScript<br>
https://book.mynavi.jp/ec/products/detail/id=142709

---

## memo
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
