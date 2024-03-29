## mysqlコンテナで日本語が入力できない問題
- 原因は細かく追求できていないが、各環境で念入りにutf8の設定を入れてなんとか解決
```bash
# コンテナ起動時
docker run --name mysql_ctr -dit -e MYSQL_ROOT_PASSWORD=mysqlrootpass -e MYSQL_DATABASE=hoge_db -e MYSQL_USER=hoge_db_user -e MYSQL_PASSWORD=hoge_db_pass mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --default-authentication-plugin=mysql_native_password

# コンテナ内のbash起動時
docker exec -e LANG=C.UTF-8 -it mysql_ctr /bin/bash

# mysql起動時
mysql -u root -p --default-character-set=utf8mb4
```

## 既に目的のイメージを使ったコンテナが存在する場合
- コンテナ名を違うものにして作成すれば、同じイメージを使っても問題ない

> 同じイメージを使用して複数のコンテナを作成することは問題ありません。各コンテナは独立した環境として動作し、他のコンテナの動作に影響を与えることはありません。

## `docker container create`(コンテナ作成)する際、イメージが無い場合は自動でPullしてくれる
- `docker pull`はイメージをダウンロードするためのもの
- `docker container create`はイメージを基にコンテナを作成するためのもの
- 通常、`docker container create`を実行する際にイメージがローカルにない場合、それを自動的にダウンロードするため`docker pull`を明示的に実行する必要は必ずしもない
- ただし、イメージを事前にダウンロードしておくことで、後のコンテナ作成のスピードアップや、必要なイメージのバージョンを確認・保持するといった目的で利用されることがある

---

## dstで指定したディレクトリが存在しない場合

- 新しく指定した名前でディレクトリが作成される
- もし存在していた場合、srcで指定したホストのディレクトリやファイルがマウントされ、元の内容は見えなくなる

---

## `--mount`オプションで使用できる主なtype
<img width="557" alt="スクリーンショット 2023-09-29 0 19 45" src="https://github.com/naon708/til/assets/77439261/7cc68377-de05-4842-b2f2-d05062af312c">

- `bind`
  - ホストの特定のファイルやディレクトリをコンテナ内のファイルやディレクトリに直接マウントする場合に使用する
  - ホスト側での変更はコンテナ側に、コンテナ側での変更はホスト側に即座に反映される
- `volume`
  - Dockerが管理する領域にデータを保存する
  - ホストマシン上の特定の場所を指定する必要はなく、Dockerが自動的に適切な場所(Docker領域)にデータを格納する
  - ボリュームはコンテナ間で共有でき、データの永続性や移植性に優れている
- `tmpfs`
  - コンテナの特定のパスに対して、メモリ上に一時的なファイルシステムをマウントする
  - ディスク上にはデータは保存されず、コンテナが停止するとデータは消失する

- 選択基準: 用途や要件（永続性、性能、共有が必要かどうか）

---

## コマンド読解
```bash
docker container create -it --mount type=bind,src="<ホストマシン側の絶対パス>",dst=/foo --name paddleocr registry.baidubce.com/paddlepaddle/paddle:2.4.2-gpu-cuda11.7-cudnn8.4-trt8.4
```
- `docker container create`: コンテナ作成
- `--mount type=bind,src="<ホストマシン側の絶対パス>",dst=/foo`: ホストマシンとコンテナ間でディレクトリやファイルを共有するための設定
  - `type=bind`: bindマウントを使用することを指示している。ホストマシンの特定のパスをコンテナ内のパスに直接マウントする
  - `src="<ホストマシン側の絶対パス>"`: ホストマシン上のマウントするディレクトリやファイルのパスを指定します。
  - `dst=/foo`: コンテナ内でのマウント先のパスを指定
- `--name paddleocr`: コンテナ名を指定
- `registry.baidubce.com/paddlepaddle/paddle:2.4.2-gpu-cuda11.7-cudnn8.4-trt8.4`: Dockerイメージを指定
  - Baiduのレジストリから、PaddlePaddleの2.4.2バージョンかつ、CUDA 11.7, cuDNN 8.4, TensorRT 8.4 に対応しているイメージを使用
  - https://www.paddlepaddle.org.cn/documentation/docs/en/install/docker/linux-docker_en.html
