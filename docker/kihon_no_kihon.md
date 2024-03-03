# これはなに
- 「Docker & Kubernetes のきほんのきほん」という書籍のメモ
- https://book.mynavi.jp/ec/products/detail/id=120304

# 本編

## Chapter 5
- ネットワークを作成→MySQLのコンテナを作成→WordPressのコンテナを作成
- MySQLコンテナを先に作ってWordPressコンテナ作成時にDB用の環境変数やらなにやらを指定する

> The WORDPRESS_DB_NAME needs to already exist on the given MySQL server it will not be created by the wordpress container.
> 
> WORDPRESS_DB_NAME は、指定された MySQL サーバー上にすでに存在している必要があります。

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
