---
title: Get Started
description: What does your user need to know to try your project?
categories: [Examples, Placeholders]
tags: [test, docs]
weight: 2
---

Artemis のインストールや使用方法など、artmis を使い始める方法をご紹介します。

Artemis は ROOT の拡張機能として実装された解析フレームワークですので、いくつかの依存した外部ソフトウェアのインストールと、解析を行うためのいくつかのファイルを準備しなくてはなりません。artemis がどのように動作するのかの簡単な例は example に格納してありますが、実際のデータを解析する際にはもう少し設定が必要になります。

このページでは artemis をインストールをする際に必要なソフトウェアと依存関係を説明し、適切なセットアップガイドを選択してインストールを開始できるようにします。

## インストール要件

ROOTは共有ライブラリを読み込むことを可能としていますが、artemis は独自のコマンドを実装するために、ソフトウェアアプリケーションとして動作します。artemis が依存するいくつかの外部ライブラリが存在しており、ユーザーは必要に応じてインストール時に選択することができます。

| 項目       | 要求             | 必須 | 動作確認                                               |
| ---------- | ---------------- | ---- | ------------------------------------------------------ |
| C++        | C++17            | o |                                                        |
| ROOT       | > 6.26.0         | o    | 6.30.06 で確認済み (2024-04-15)                        |
| yaml-cpp   | > 0.6.3          | o    | 0.7.0 では pkgconfig の場所がおかしいという報告もあり  |
| openmpi    | 未確認           |      | 計算機クラスタにインストールされているものは使えるはず |
| GetDecoder | バージョンなし？ |      | メモリリークの報告あり。確認中。                       |

## 前提

- C++17 に対応したコンパイラ
- ROOT-6 (最小の枝番は未確認。6.30.06 でも動作はします。)

```
root-config --version
```

とやって ROOT が使える状態であることを確認してください。使えない場合にはインストールされていないか、環境変数の設定ができていません。
[ROOTをインストール](https://root.cern/install/)するか、グループの誰か、サーバーの管理者などに初期設定の仕方を教えてもらいましょう。

- yaml-cpp 0.6.3 (0.7.0 でも動作しますが、pkgconfig の場所がおかしいようでコンパイルにひと手間かかります)
- OpenMPI にも対応しています。
- GETDecoder にも対応しています。

## インストール

- ROOT-6 を使用する場合には develop ブランチを使用します。
- [github](https://github.com/artemis-dev/artemis/tree/develop) からソースコードをもってきてコンパイルをします。

```shell
mkdir artemis-work
cd artemis-work
git clone --branch develop https://github.com/artemis-dev/artemis.git artemis-src
cmake -DCMAKE_INSTALL_PREFIX=$HOME/local/artemis -DCMAKE_PREFIX_PATH=$HOME/local/yaml-cpp/  -B artemis-build -S artemis-src
cmake --build artemis-build --target install -j10
```

これで $HOME/local/artemis にインストールされたはずです。
以下を実行することで環境変数が設定されます。スクリプトは .bashrc などに記入することで自動で実行できるようになります。

{{< tabpane text=true >}}
  {{% tab header="thisartemis.shを呼び出す:" disabled=true /%}}
  {{% tab header="bash, zsh"  %}}

  ```
  pushd $HOME/local/artemis/bin && . ./thisartemis.sh && popd
  ```

  {{% /tab %}}
  {{% tab header="csh, tcsh"  %}}

  ```
  pushd $HOME/local/artemis/bin && source ./thisartemis.csh && popd
  ```

  {{% /tab %}}
{{< /tabpane >}}

## 解析ディレクトリの準備

artemis 自身は特に環境変数は要求しませんが、git を利用したコード管理をやりやすくするために、環境変数を設定していることがあります。

artemis が推奨するディレクトリ構造は以下の通りです。ここで output や rawdata はシンボリックリンクになっています。
必ずしも必要なリンクではありませんが、特に output はローカルディスクを使い切ることの内容に事前の対策として作成することが多いディレクトリになっています。

```
.
├── artemislogon.C
├── conf
│   ├── map
│   └── seg
├── hist
│   └── pla
├── macro
├── output -> /data/work/ota/output
├── prm
├── rawdata -> /home/nestdaq/run/data
└── steering
    └── itmpl.
```

### artemislogon.C

artemis 用の初期設定ファイルになっています。コマンドやモジュールデコーダの登録やパラメータ変更に用います。

### steering

解析の起点となるファイルを配置するディレクトリです。

### conf

検出器の設定やチャンネルマップなどの対象が検出器ごとなど大きなまとまりに対する設定ファイルを格納する

### prm

チャンネル単位、物理量単位などの具体的に演算対象となる単位に対するパラメータを記述するファイルを格納する

### hist

ヒストグラムの設定ファイルを格納する

### macro

マクロなどを格納する
