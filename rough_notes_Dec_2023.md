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
