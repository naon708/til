## memo
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
