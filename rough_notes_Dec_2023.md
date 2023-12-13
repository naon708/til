## 【セキュリティ】`TOTP`(`Time-based One-Time Password`)
- ざっくり、ワンタイムパスワード(の生成手順)のこと。
- PC上で2FAを行いたい場合は[KeePassXC](https://keepassxc.org/)のようなツールもある。

### TOTPに対応する認証アプリケーション
- Google Authenticator
- Microsoft Authenticator
- など

---

## YAMLファイルの`&`ってなんだ
- 「アンカー」
- アンカーを定義することで値を使い回せる。YAMLファイルをDRYに保つための記述。
```yaml
ja:
  employment_status: &employment_status
    normal: 通常勤務
    absent: 休職中
    retired: 退職済み
  something_else:
    hoge: ほげ
    <<: *employment_status
```

---

## 【Ruby】配列からblankやfalsyな要素を除去する方法
- Rubyの`compact`メソッド
- Railsの`compact_blank`メソッド(Rails6.1から追加)
```ruby
arr = [1, "", nil, 2, " ", [], {}, false, true]

# nilのみ除去
[1, "", nil, 2, " ", [], {}, false, true].compact
=> [1, "", 2, " ", [], {}, false, true]

# nilに加えてblankやfalsyな要素も除去(※Rails専用)
[1, "", nil, 2, " ", [], {}, false, true].compact_blank
=> [1, 2, true]
```

---

## 【JS】配列からfalsyな要素を除去する方法
- nullやundefinedなどのfalsyな要素を除去したいとき、`filter`を使うのが常識らしい
```js
const words = ['foo', 'bar', null, 'baz', undefined, false, 'hoge']
words.filter((word) => word)

=> ['foo', 'bar', 'baz', 'hoge']
```

---

## 【Rails】関連先モデルの一覧を取得
```ruby
Customer.reflect_on_all_associations.map(&:name)
```

---

## 【Vue】毎回忘れる`v-slot`ディレクティブ
```html
<main-container>
  <template #header>
  <!-- v-slot:header の省略 -->
    <breadcrumb />
  </template>
  <template #default>
    <main-frame></main-frame>
  </template>
  <template #footer>
    <button>Close</button>
  </template>
</main-container>
```

- templateタグの親要素が元となるコンポーネントで、そこでslotが定義されている(この場合、`<main-container>`)
- 「このtemplateはどこに挿入されるんだ？」が気になったら親要素の定義ファイルを見に行く(この場合、`main-container.vue`みたいなファイル)

```html
<!-- main-container.vue -->

<section>
  <div>
    <!-- @slot ヘッダー -->
    <slot name="header" />
  </div>
</section>

<section>
  <!-- @slot メインコンテンツ -->
  <slot />
</section>

<section>
  <div>
    <!-- @slot フッター -->
    <slot name="footer" />
  </div>
</section>
```

> name を持たない <slot> アウトレットは、暗黙的に "default" という name を持つものとされます。

https://ja.vuejs.org/guide/components/slots

---

## 【Web】オフラインモードってどうなってるの？
1. サーバーから取得したデータをブラウザのローカルストレージに保存
2. オフラインでアクセスした場合はローカルストレージのデータを表示
3. 次回オンラインアクセス時に、ローカルストレージのデータを更新(同期)

![オフラインモード](https://github.com/naon708/til/assets/77439261/d496ce8e-cd86-4177-878c-c6c6cadce796)

- 最後にサーバーと通信した時点でのデータが利用可能。オフラインの間に他のユーザーやプロセスによってデータが更新されていても反映されない。
- オフラインではサーバーと通信できないため、POSTリクエスト（情報の更新）は行えない。

### memo
- LocalStorageのデータは`同一オリジン(ドメイン、プロトコル、ポート)`のページで共有される
- Cookieより容量が大きい

---

## 【AI】シーケンス図の作り方
- プロンプトに「シーケンス図をPlantUML形式で書いてください」と加えると生成してくれる
- https://www.plantuml.com/plantuml にコピペして確認する
```plantuml
@startuml
skinparam actorStyle awesome

actor ユーザー as User
participant "クライアント\nアプリケーション" as Client
database "ローカル\nストレージ" as LocalStorage
database "リモート\nサーバー" as Server

User -> Client : アプリを使用する

note over Client, Server: インターネットがオフラインの場合
Client -> LocalStorage : データを読み込む
LocalStorage -> Client : データを返す

note over Client, Server: インターネットがオンラインの場合
Client -> Server : データを同期する
Server -> Client : 最新のデータを返す
Client -> LocalStorage : 最新データを保存

Client -> User : データを表示する

@enduml
```

<details><summary>生成される画像</summary>

![オフラインモード](https://github.com/naon708/til/assets/77439261/cec59276-4f4a-4a51-88d5-b8bd7eddcd7e)

</details> 
