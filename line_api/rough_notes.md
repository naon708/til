## デバッグするためにログ出力できるようにする
- スプシに出力する方法が上手くいかなかったのでGCPに紐づけてログ出力
　　- 参考: https://zenn.dev/nenenemo/articles/db5f4d3930276c
- 紐づけできたら　`console.log` を仕込んで `clasp push && clasp deploy -i [deployment_id]` でURLを変えずにデプロイ
