---
title: "cdk initしよう"
---

# はじめに

本章よりCDKの開発手順について示します。Cloud9を使ってCDKを用いたコーディング作業を行っていきます。CDKでの開発を行うためには[aws-cdk](https://www.npmjs.com/package/aws-cdk)をインストールする必要がありますが、Cloud9は予めCDK開発のために必要なパッケージがインストールされています。

# まずは`cdk init`

まずはCloud9のターミナルを開いてCDKのプロジェクトを作成します。プロジェクトを立ち上げるために空のフォルダを作成します。

```bash
mkdir cdk-bot-handson
cd cdk-bot-handson
```

プロジェクト用のフォルダに移動したらプロジェクトを立ち上げます。

```bash
cdk init --language typescript
```

`cdk init`はCDKプロジェクトを立ち上げるためのコマンドです。ここでは２つの引数`language`と`typescript`が入力されています。`language`を指定することでCDKで開発するためのプログラミング言語（ここでは`TypeScript`）を選択します。プロジェクトの立ち上げが終わったらいくつかのファイル・フォルダが生成されます。おそらく以下のような構成になるかと思います。

```
├── README.md
├── bin
│   └── cdk-bot-handson.ts
├── cdk.json
├── lib
│   └── cdk-bot-handson-stack.ts
├── node_modules
├── package-lock.json
├── package.json
├── test
│   └── cdk-bot-handson.test.ts
└── tsconfig.json
```

今回のCDKでの開発では`lib/cdk-bot-handson-stack.ts`のみを扱います。ちなみに`lib`下で記述されたコードが**Stack**で、`bin`下で記述されたコードが**Stack**の集まりである**App**に対応します。本ハンズオンではスタックは一つしか作成しないので、`bin`下のコードは追加で実装する必要はありません。

# CDKコマンドツールについて

本ハンズオンで用いるCDKコマンドについて説明します。

- cdk init
  - CDKプロジェクトを作成します
- cdk synth
  - プログラミング言語で記述されたスタックをCloudFormation形式のテンプレートファイルに変換します
- cdk bootstrap
  - 初めてAWSへスタックをデプロイするときに実行します
- cdk deploy
  - スタックをAWSへデプロイします
- cdk destroy
  - スタックをAWSから削除します

CDKコマンドツールの詳細については[こちら](https://docs.aws.amazon.com/cdk/latest/guide/cli.html)を参照してください。

# TypeScriptのコンパイル

今回CDKのプロジェクトをTypeScriptで立ち上げたので、TypeScriptで記述されたコードをJavaScriptにコンパイルする`tsc`コマンドを実行するためのスクリプトが`package.json`ファイルに用意されています。以下のコマンドを別のターミナルで実行することでJavaScriptへのコンパイルをリアルタイムで行うスクリプトが実行されます。

```bash
npm run watch
```

JavaScriptへのコンパイルを都度実行したい場合は以下のコマンドを実行します。

```bash
npm run build
```

TypeScriptで記述されたスタックは[ts-node](https://github.com/TypeStrong/ts-node)を使って直接TypeScriptを実行するのでコンパイルの必要はありませんが、以降の章で実装するLINE BOTのためのアプリケーションコードはNode.js環境下で実行されるのでJavaScriptへのコンパイルが必要になります。また常時コンパイルを実行することで、構文ミスの早期発見にもつながるので`npm run watch`の実行をオススメします。

# `cdk synth`でテンプレートに変換

この章ではまだスタックの実装は行っていませんが、試しに以下のコマンドを実行してデプロイ用のテンプレートファイルを作成してみます。

```bash
cdk synth
```

ターミナル上にCloudFormation形式で書かれたコードが表示されます。また新たに`cdk.out`というフォルダが生成されます。このフォルダの中にデプロイ用のテンプレートファイルが保存されています。

# `cdk bootstrap`を実行する

CDKで初めてAWS環境下にスタックをデプロイするときに以下のコマンド実行する必要があります。

```bash
cdk bootstrap
```

厳密には、アセットを使用しておらず尚且サイズが51,200バイト未満のテンプレートであればこのコマンドを実行する必要はないみたいですが、最初のコマンド実行のみで以降の開発（別プロジェクト含む）では実行する必要はないので今回実行しておくことをオススメします。ただし開発リージョンを変える場合はそのリージョンに対しての最初のコマンド実行で`cdk bootstrap`が必要になります。

次章から実際にLINE BOTとスタックの実装を行っていきます。