# これはなに
- 「Docker & Kubernetes のきほんのきほん」という書籍のメモ
- https://book.mynavi.jp/ec/products/detail/id=120304

# 本編
## Chapter 6

### Docker Hub への登録
- インターネット上にあるイメージの置き場のことを「Dockerレジストリ」と呼ぶ
- レジストリは個人や会社単位で無数に存在していて、その中でもDocker社が公式で運営しているレジストリが「Doker Hub」

### コンテナの改造
- コンテナ内にファイルを追加したりソフトウェアをインストールしたりといった要件がある場合
- ファイルの追加系はコピーやマウントを使って実現できるが、コンテナ内にLinuxのソフトウェアをインストールしたりコンテナの設定ファイルをいじったりは、コンテナに入って実行

### コンテナのイメージ化
- 方法は2種類あって、`docker commit`を使って既存のコンテナをイメージ化する方法と、Dockerfileからイメージを生成する方法
- Dockerfileでイメージ化
  - Dockerfile作成→`FROM`でベースとなるイメージを指定したり、`COPY`でローカルのファイルをコンテナ内のディレクトリにコピーしたい旨を記述したり、`RUN`でコンテナ(Linux)内で実行したいコマンドを記述したり。（要はイメージを作る際の仕様書みたいな感じ）
  - `docker build -t [イメージ名] .`実行(Dockerfileが存在するディレクトリで実行するとコマンドがシンプルになる)
  - そうすると作ったイメージを元にこれまで通りコンテナを起動できる
### mount

- mount: マウント = 据え付ける(物を置いて固定させること)
- volume: ハードディスク(記憶領域)の一部分

---

- 「ボリュームをマウントする」
  - ボリュームとは、コンテナ内の記憶領域の一部分のこと
  - どこかしらにデータを永続化させる必要があるため(データの置き場が必要)
  - コンテナは「作っては壊す」という性質で、コンテナを破棄するとコンテナ内のデータは消えるため
 
---

#### ボリュームマウント
- Dokcer Engineが管理している領域内にボリュームを作成してコンテナにマウントする

  ```bash
  docker run --name apache_ctr -d -p 8090:80 -v apache_vol:/usr/local/apache2/htdocs httpd

  docker inspect apache_ctr
  # 出力結果の抜粋
  "Mounts": [
      {
          "Type": "volume",
          "Name": "apa000vol1",
          "Source": "/var/lib/docker/volumes/apa000vol1/_data",
          "Destination": "/usr/local/apache2/htdocs",
          "Driver": "local",
          "Mode": "z",
          "RW": true,
          "Propagation": ""
      }
  ],
  ```
- マウントされているか確認する方法(inspectではなく目視したい場合)
  - まずボリュームマウントの確認方法はバインドマウントに比べてややこしい
    - バインドマウントだったら、ファイル追加・削除などして、ローカルとコンテナを行き来して差分を見れば確認できる
    - ボリュームマウントの場合はコンテナ経由でしかボリュームの中身を操作できないから一手間必要
  - ケース1(実運用向き)
    - コンテナAにマウントしたボリュームを確認したいとして、コンテナAのマウント対象のディレクトリにファイルを作ってみる。
    - 続いてLinuxだけ入ったコンテナBを起動して、同じボリュームに接続してファイルが存在するか確認する
  - ケース2(コンテナ削除するため学習などで)
    - コンテナAにボリュームをマウントして、ファイルを作ってみる
    - 続いてコンテナAを削除し、新たにコンテナBを起動して同じボリュームをマウントしてファイルが存在するか確認する

##### ケース2
- [x] コンテナAのマウント済みディレクトリにファイルがいくつかあることを確認
- [x] コンテナAを削除
- [x] コンテナBを起動してボリュームをマウントする
- [x] コンテナBのマウント済みディレクトリに同じファイルが存在することを確認

##### ケース1
- [x] コンテナAのマウント済みディレクトリにファイルがいくつかあることを確認
- [x] Linuxだけ入ったコンテナBを起動してボリュームにマウントする
- [x] ファイルが存在するか確認

```bash
# busyboxは軽量で最低限の機能を提供するLinuxのみのコンテナ
docker run -it --rm -v apa000vol1:/tmp busybox
cd tmp && ls
```

#### マウントデータのバックアップ方法(バックアップの実務上の重要度ははっきりわかってない)
- バインドマウントの場合は、マウントしたディレクトリをコピーしておけばOK
- ボリュームマウントは、ボリューム自体をコピーできないのでひと手間かかる
  - コンテナAにボリュームをマウントする
  - バックアップ用コンテナB(busybox)の`src`ディレクトリにボリュームをマウントする　←ここでコンテナAとBが接続された
  - コンテナBの`dest`ディレクトリにバックアップ(出力)したいホストマシンのディレクトリをバインドマウントする
  - `run`コマンドの引数で`tar`コマンドを実行し、`src`から`dest`へ圧縮保存する
  - めでたくコンテナAにマウントしたボリューム内の情報が`src`→`dest`を介してローカルに保存される
 
```bash
# メインのコンテナをボリュームマウントしておく
docker run --name apache_ctr3 -d -p 8093:80 -v apa000vol1:/usr/local/apache2/htdocs httpd
# メインのコンテナ内に try_volume_backup というファイルをテストファイルとして作っておく
docker exec -it apache_ctr3 /bin/bash
# 作成作業
cd htdocs/
ls
#=> index.html           test2.html
touch try_volume_backup
exit

# busybox　を使って、メインコンテナにマウントしたボリュームからデータをローカルの $PWD/backup/ 配下に流す(バックアップする)
docker run --rm -v apa000vol1:/src -v $PWD/backup/:/dest busybox tar czvf /dest/backup_files.tar.gz -C /src .
#=>
# *********************
# ./
# ./try_volume_backup
# ./test2.html
# ./index.html
# *********************

# バックアップできているか確認
cd backup
ls
#=> backup_files.tar.gz

# 解凍して中身を確認
tar xzvf backup_files.tar.gz
ls
#=> backup_files.tar.gz  index.html           test2.html           try_volume_backup ←ローカルにバックアップされてる！

```

#### バインドマウント
- 物理マシン内の特定のディレクトリをコンテナにマウントする
- runコマンドの`-v`オプションでマウント元とマウント先を指定するだけ

---

- apache(httpd)の　htdocs/　配下にファイルを置くとファイル名がリンクになってクリックするとファイル名のパスに遷移した
  - ディレクトリを一覧化して各ファイル名をパスに指定したリンクを生成するapacheの機能らしい
  - メリットはよくわからなかった

### copy
```bash
docker cp ./index.html some_ctr:/usr/local/hoge/index.html
```


## Chapter 5

### 【memo】Redmine と MySQL のコンテナ構築
```bash
# sample command

# mysql
docker run -d --name some-mysql --network some-network -e MYSQL_USER=redmine -e MYSQL_PASSWORD=secret -e MYSQL_DATABASE=redmine -e MYSQL_RANDOM_ROOT_PASSWORD=1 mysql:5.7
# redmine
docker run -d --name some-redmine --network some-network -e REDMINE_DB_POSTGRES=some-postgres -e REDMINE_DB_USERNAME=redmine -e REDMINE_DB_PASSWORD=secret redmine
```
| 項目 | 値 |
|--------|--------|
| ネットワーク | `redmine_net` |
| MySQLコンテナ名 | `mysql_ctr` |
| Redmineコンテナ名 | `redmine_ctr` |
| MYSQL_ROOT_PASSWORD | `mysqlrootpass` |
| MYSQL_DATABASE | `redmine_db` |
| MYSQL_USER | `redmine_db_user` |
| MYSQL_PASSWORD | `redmine_db_pass` |
| ポート | `8088:3000` |

### WordPressコンテナ作成
- 「アプリケーション本体(プログラム)+プログラム実行環境+Webサーバー」のセットになっているイメージを使う(というか公式のWordPressイメージがそうなっている)
- このようなセットになっているコンテナとDBコンテナを組み合わせた運用はよくあるらしい

```
# memo
test-user
iR3Yg(hI3!zcQHsOV4
```

### MySQLコンテナ作成
- ルートパスやDB名など、指定する環境変数が多い

[参考](https://hub.docker.com/_/mysql#Environment%20Variables:~:text=tag%20%2D%2Dverbose%20%2D%2Dhelp-,Environment%20Variables,-When%20you%20start)

### ネットワーク作成
```
docker network create wordpress000net1
```

```bash
# 詳細確認
docker network inspect a3bb407274bc                                                                                      ✔
...
"Containers": {},
...
```
作成したネットワーク上にコンテナを構築した後↓↓
```bash
"Containers": {
    "358ae7e8cfcac886405b1f3740ac0de39a0894f893beab1e57958f78f00cb6a5": {
        "Name": "wordpress000ex12",
        "EndpointID": "ff69ad7ae60e06f7cb45d0f3efd9c610e0fdfa15e0093758840f959cecddca15",
        "MacAddress": "02:42:ac:16:00:03",
        "IPv4Address": "172.22.0.3/16",
        "IPv6Address": ""
    },
    "9c0b356b1c9c3eea70c140087804c6893df5c3dc461a7fc159bc38d7bc93c247": {
        "Name": "mysql000ex11",
        "EndpointID": "cff70db56c748fcd527f9a07d71dc1c80d81cec6072fbba1abd20eca87724e11",
        "MacAddress": "02:42:ac:16:00:02",
        "IPv4Address": "172.22.0.2/16",
        "IPv6Address": ""
    }
},
```

---

### WordPress構築の流れ
- ネットワークを作成→MySQLのコンテナを作成→WordPressのコンテナを作成
- MySQLコンテナを先に作ってWordPressコンテナ作成時にDB用の環境変数やらなにやらを指定する

> The WORDPRESS_DB_NAME needs to already exist on the given MySQL server it will not be created by the wordpress container.
> 
> WORDPRESS_DB_NAME は、指定された MySQL サーバー上にすでに存在している必要があります。


---

- LAMP構成(Linux/Apache/MySQL/PHP)
- Webアプリケーションの構成は大体これで、ApacheがNginxに代わったり、MySQLがPostgreSQL、PHPがRubyに代替されたりするが抽象化すると「LinuxOS+Webサーバー+RDBMS+アプリケーション」になってる(SPAだとフロントとAPIでもう１つ分かれるかな)

## Chapter 4

```
docker ps

CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
a06bd2466ce7   nginx     "/docker-entrypoint.…"   4 seconds ago   Up 3 seconds   0.0.0.0:8080->80/tcp   nginx000ex7
```

---

- コンテナを停止していないとコンテナを破棄できない
- コンテナを破棄していないとイメージを破棄できない

---

- docker exec でコンテナ入ってhtmlファイル編集したら変更が反映されたけど、コンテナ破棄して再び起動すると差分は無くなっていた。
- 「使っては捨てる」というコンテナの性質があるうえでマウントしていないのでそれはそう。

---

- Apacheが動いているサーバーにファイルを配置すればアクセスできそう
- ただこれまでの起動方法(オプションなし)だと、コンテナに外からアクセスできないようになっている（これはDockerの仕様だと思う）
  - Apache自体は80番ポートで通信を受け付けているが、物理マシンの外と繋がっていないのでApacheに届くことはない
- なので起動時にポートの指定が必要


---

- xxxというイメージを元にyyyというコンテナを起動(作成・開始)する
- `docker run --name yyy -d xxx`

```
# ローカルに httpd のイメージが見つからないのでダウンロードした
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
f546e941f15b: Pull complete
Status: Downloaded newer image for httpd:latest
```

---

- ボリューム (`docker volume`)
  - Docker Engine の上に乗っかっている記憶領域
  - コンテナは停止するたびに壊すので、そのままだとデータを永続化出来ない
  - なので、ボリュームにマウントして永続化させる(バインドマウントの場合もある)
- ネットワーク (`docker network`)
  - コンテナ同士を接続するためのもの
  - 接続しないとRailsコンテナからDBコンテナにアクセスしたりできなさそう

---

- `docker start` とか `docker run` コマンドは、 `container` (上位コマンド)が省略されている
```bash
# 下記は同義だが、上がコマンド再編成後の新しい書き方なのでそちらで慣れておきたい
docker container run
docker run
```

## Chapter 3

EC2でDockerを使う手順
https://book.mynavi.jp/files/user/support/9784839972745/docker_tokuten_appendix9-10.pdf#page=8

---

- Dockerを使うには主に3つの方法がある
  - LinuxOS が入った物理マシンで使う
  - Macなどの物理マシンに仮想環境(Linuxの仮想マシン)を構築してその上でDockerを使う
    - EC2などのクラウド(レンタル環境)を使う手もある
  - Docker for Mac を使う

---

↓この角括弧のプロンプトの書き方はLinuxであることを表していたのか。。
```bash
# Mac
ComputerName:~ UserName $

# Linux
[UserName@HostName~]#
```

## ~ Chapter 2
- まず大前提
  - DockerはLinuxOS上でしか動かないし、コンテナ内にはLinuxのソフトウェアしかインストールできない
  - 普段MaｃでDockerを使えているのは、Docker for Macというデスクトップアプリが仮想のLinux環境を提供してくれているから
  - VirtualBox等の仮想環境との違い
  　　- ホストOSの上にゲストOSを乗っけるのではなく、ホストOSと同じレイヤー・・・
- 全体感・大枠
  - Dockerで概念的に一番外側に位置するのがDocker Engine
  - 物理マシン > LinuxOS > Docker Engine > コンテナ > Linuxディストリビューション(Linuxっぽいもの) > Linux用のソフトウェア(nginx, PostgreSQL, ...)
  - ディストリビューションがDocker Engineを介してLinuxカーネルに指示を伝えてマシンを動かす
- メリット
  - 独立している
  - Docker自体にカーネルがないので軽い
- イメージからコンテナを作成する
  - イメージを共有すれば他のマシンでも同様の環境を構築できる
  - コンテナを改造して新たなイメージを作ることもできる
  - 基本的にはDocker Hubなどからイメージをpullしてきて、開発するプロダクトに合わせてカスタマイズしてイメージ化し、それをチームで共有して開発するはず
- Dockerのライフサイクル
  - 作成→起動→停止→破棄(create->start->stop->remove)
  - create と start をまとめて run としたりする

# おまけ
## `<none> のイメージ`
- たまに`<none>`というイメージが出来ていることがある
  - これはイメージ名が重複している場合にできるもの(重複したイメージ名はbuildできない仕様)
  - 同名のイメージをbuildしようとしたときに、古いものが`<none>`に置き換わる
  - 特に必要ない場合がほとんどらしく、不要なイメージは容量圧迫するので削除すると良い(`none`のイメージを参照しているコンテナがある場合は削除できないが、その場合はエラーで教えてくれる)

```
docker image ls -f "dangling=true" -q                                                                                      ✔
07259e822fbb
94232dc1d70c
...

docker image rm $(docker image ls -f "dangling=true" -q)
```
