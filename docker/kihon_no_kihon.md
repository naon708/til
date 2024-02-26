# これはなに
- 「Docker & Kubernetes のきほんのきほん」という書籍のメモ
- https://book.mynavi.jp/ec/products/detail/id=120304

# 本編
- まず大前提
  - DockerはLinuxOS上でしか動かないし、コンテナ内にはLinuxのソフトウェアしかインストールできない
  - 普段MaｃでDockerを使えているのは、Docker for Macというデスクトップアプリが仮想のLinux環境を提供してくれているから
  - VirtualBox等の仮想環境との違い
  　　- ホストOSの上にゲストOSを乗っけるのではなく、ホストOSと同じレイヤー・・・
- 全体感・大枠
  - Dockerで概念的に一番外側に位置するのがDocker Engine
  - 物理マシン > LinuxOS > Docker Engine > コンテナ > Linuxディストリビューション(Linuxっぽいもの) > Linux用のソフトウェア(nginx, PostgreSQL, ...)
  - ディストリビューションがDocker Engineを介して
- メリット
  - 独立している
  - Docker自体にカーネルがないので軽い
- イメージからコンテナを作成する
  - イメージを共有すれば他のマシンでも同様の環境を構築できる
  - コンテナを改造して新たなイメージを作ることもできる
  - 基本的にはDocker Hubなどからイメージをpullしてきて、開発するプロダクトに合わせてカスタマイズしてイメージ化し、それをチームで共有して開発するはず
