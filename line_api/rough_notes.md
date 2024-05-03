## アクションイベント
### 日時選択イベント
- ↓のような感じで返ってくる
- postback の中の paramsプロパティ の中にLINEトーク画面で選択した日時の情報が入っている
- 日時選択アクションの中で `postback.data` も指定できる
```json
[
    {
        "webhookEventId": "fjsdoafjoijsjd***********",
        "source": {
            "type": "user",
            "userId": "sfdojfosfsjfdlsjfldsfh******"
        },
        "mode": "active",
        "replyToken": "dlsfjdlkfjslkfsdjflsjl********",
        "timestamp": 1714729715.281,
        "postback": {
            "data": "storeId=12345",
            "params": {
                "datetime": "2017-12-25T00:03"
            }
        },
        "type": "postback",
        "deliveryContext": {
            "isRedelivery": false
        }
    }
]
```

---

## ナローキャストメッセージ
- ナローキャストメッセージの仕様とユースケースの解像度がまだ低い
  - オーディエンスを指定して送るもの
 
### オーディエンスとは
- 何らかの条件でユーザーをグルーピングできる。`1グループ = 1オーディエンス`。
- LINE Bot の管理画面で作る。
  - https://manager.line.biz/account/@139xbuew/broadcast/audience

<img width="1146" alt="スクリーンショット 2024-04-14 23 51 56" src="https://github.com/naon708/til/assets/77439261/c0cd8af7-fdf7-4c23-a02c-2a096b8c0099">

### 公式アカウントにある程度メンバー数がないと送れない
- そもそも絞り込み対象のユーザーが100人以上いないとエラー
- 色々絞り込んで最終的にメッセージを送信する対象が50人以上いないとエラー
- (例外を除く)
- 詳しくは→ https://developers.line.biz/ja/reference/messaging-api/#send-narrowcast-message

## オウム返し実装時のメモ
- LINE Developer 画面で設定した Webhook URL = GASのデプロイURL
- 公式LINEのメンバーアカウントでテキストメッセージ送る→LINE API が受取り Webhook URL に指定したURLにWebhookを送信する→GASコードが受取り、`チャネルアクセストークン`を検証したり返信文章を生成して　LINE API　にレスポンスする
- `userId` -> プロバイダー毎にユーザーを識別するためのID。 `LINE ID` とは別物。

### GAS が LINE API　にレスポンスするところ
- CHANNEL_ACCESS_TOKEN(= チャネルアクセストークン)はチャネル(= 公式アカウントと同じ単位と考えるとわかりやすい)に設定されているトークン
  ```js
    const option = {
      'headers': {
        'Content-Type': 'application/json; charset=UTF-8',
        'Authorization': 'Bearer ' + CHANNEL_ACCESS_TOKEN,
      },
      'method': 'post',
      'payload': JSON.stringify({
        'replyToken': reply_token,
        'messages': [{
          'type': 'text',
          'text': responseText,
        }],
      }),
    }
  ```

- LINE API のエンドポイントに fetch して返す
  - `https://api.line.me/v2/bot/message/reply`
  - 参考: https://developers.line.biz/ja/reference/messaging-api/#send-reply-message
  ```js
  UrlFetchApp.fetch(LINE_API_ENDPOINT, option);
  ```

## GASコード内で使う環境変数は設定から
- プロジェクト画面の「設定」から `スクリプト プロパティを追加` 

## `e.postData.contents` の中身
- `contents` の他に `"contextPath":"","queryString":""` も含まれてた 
```json
{
  "contents": {
    "destination": "KO94cd55lfi93c16ad834add93bd6756d",
    "events": [
      {
        "type": "message",
        "message": {
          "type": "text",
          "id": "5027638473738450036",
          "quoteToken": "KO94cd55lfi93c16ad834add93bd6756dZpPyJTJusYVtvzNiC4h_-Z_WsC_xtZYFpYl6uTPzO0mtsgl0NDAJZ7S4Zhof0JMaex506a3yxoqwwb50P56YJ6V_bmaErj1op7uOMA1ja5ltmIn-C9qg35A",
          "text": "あ"
        },
        "webhookEventId": "01HTWGPZ47VEKDOEI99098DN6MGS",
        "deliveryContext": {
          "isRedelivery": false
        },
        "timestamp": 1712501521036,
        "source": {
          "type": "user",
          "userId": "KO94cd55lfi93c16ad834add93bd6756d"
        },
        "replyToken": "KO94cd55lfi93c16ad834add93bd6756d",
        "mode": "active"
      }
    ]
  }
}
```


## そのうち ngrok が必要になる気がするので参考リンク
- https://okash1n.works/posts/how-to-get-a-webhook-request-in-local-smartly-using-ngrok/

## デバッグするためにログ出力できるようにする
- スプシに出力する方法が上手くいかなかったのでGCPに紐づけてログ出力
  - 参考: https://zenn.dev/nenenemo/articles/db5f4d3930276c
- 紐づけできたら　`console.log` を仕込んで `clasp push && clasp deploy -i [deployment_id]` でURLを変えずにデプロイ
