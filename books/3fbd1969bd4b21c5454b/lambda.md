---
title: "Lambdaを実装しよう"
---

# はじめに

この章では[AWS Lambda](https://aws.amazon.com/jp/lambda/)を使ってLINE BOTのアプリケーションを実行するサーバーレスサービスを立ち上げます。

# Lambdaとは

AWSが提供するサーバーレスのサービスです。Lambdaを用いることでサーバー管理が不要になったり、コード実行を自動でスケールしてくれたりなど様々なメリットを得ることができます。Lambdaを用いたコード開発の方法はいくつかありますが、今回はコンテナ化されたアプリケーションをアップロードする方法を用いることにします。

# アプリケーションの実装

別のターミナルを開いて以下のコマンドを実行します。

```bash
cd cdk-bot-handson
mkdir -p lambda/bot
cd lambda/bot
```

以降アプリケーションは`lambda/bot`ディレクトリの中で実装していきます。まずは`package.json`を作成します。

```bash
npm init
```

CLI上でいくつかの回答を求められますが、すべてデフォルト（エンターキー連打）で大丈夫です。`package.json`が作成できたら必要なパッケージをインストールします。

```bash
npm install --save @line/bot-sdk @types/aws-lambda aws-lambda axios dayjs
```

次にコードを実装します。

```typescript:lambda/bot/index.ts
import axios from 'axios';
import * as Line from '@line/bot-sdk';
import * as Types from '@line/bot-sdk/lib/types';
import * as Lambda from 'aws-lambda';
import * as dayjs from 'dayjs';

interface AnimeData {
  id: string
  title: string
};

const channelAccessToken = process.env.CHANNEL_ACCESS_TOKEN!;
const channelSecret = process.env.CHANNEL_SECRET!;

const config: Line.ClientConfig = {
  channelAccessToken,
  channelSecret,
};

const client = new Line.Client(config);

const getAnimeDatas = async (year: number): Promise<AnimeData[]> => {
  const res = await axios.get(`http://api.moemoe.tokyo/anime/v1/master/${year}`);
  return res.data;
}

const eventHandler = async (event: Types.MessageEvent): Promise<any> => {
  if (event.type !== 'message' || event.message.type !== 'text' || !event.source.userId) {
    return null;
  }
  if (event.message.text === '今年のアニメは？') {
    const animeDatas = await getAnimeDatas(dayjs().year());
    return client.replyMessage(event.replyToken, { type: 'text', text: animeDatas.map(animeData => animeData.title).join('\n') });
  } else {
    return client.replyMessage(event.replyToken, { type: 'text', text: '「今年のアニメは？」と聞いてね' });
  }
};

export const handler = async (proxyEevent: Lambda.APIGatewayEvent) => {
  const body: Line.WebhookRequestBody = JSON.parse(proxyEevent.body!)
  await Promise
    .all(body.events.map(async event => eventHandler(event as Types.MessageEvent)))
    .catch(err => {
      console.error(err.Message);
      return {
        statusCode: 500,
        body: 'Error'
      };
    });
  return {
    statusCode: 200,
    body: 'OK'
  };
};
```

今回はコードについての詳しい解説は省略させていただきます。今回重要になるのは以下のコード上部あたりの２つの変数になります。

```typescript
const channelAccessToken = process.env.CHANNEL_ACCESS_TOKEN!;
const channelSecret = process.env.CHANNEL_SECRET!;
```

この`CHANNEL_ACCESS_TOKEN`と`CHANNEL_SECRET`はLambdaに与える環境変数です。それぞれにチャネル作成時に取得した**チャネルアクセストークン**と**チャネルシークレット**を設定します。この環境変数の設定は次節のCDKの実装にて行っていきます。
次にアプリケーションをコンテナ化するためのDockerfileを作成します。

```Dockerfile:lambda/bot/Dockerfile
FROM public.ecr.aws/lambda/nodejs:12

COPY package.json ./
RUN npm install
COPY . .

CMD ["index.handler"]
```

# CDKの実装

以下のコマンドを実行してLambdaのためのパッケージをインストールします。

```bash
npm install --save 	@aws-cdk/aws-lambda
```

ここで注意してほしいこととして、CDKに関するnpmパッケージはすべて同じバージョンにそろえている必要があります（バージョンが異なっているパッケージを用いるとコンパイルエラーが起こります）。例えば`package.json`の`dependencies`が以下のようになっているとき、

```json
{
  "dependencies": {
    "@aws-cdk/core": "1.85.0",
    "source-map-support": "^0.5.16"
  }
}
```

`@aws-cdk/aws-lambda`は同じバージョン、`1.85.0`を指定する必要があります。

```bash
npm install --save @aws-cdk/aws-lambda@1.85.0
```

パッケージのインストールが完了したら`lib/cdk-bot-handson-stack.ts`を開きます。`The code that defines your stack goes here`というコメントアウト文が見つかるかと思います。この下にCDKのコードを実装していきます。

```typescript:lib/cdk-bot-handson-stack.ts
import * as cdk from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';

export class CdkBotHandsonStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // The code that defines your stack goes here
    new lambda.DockerImageFunction(this, 'bot', {
      code: lambda.DockerImageCode.fromImageAsset('./lambda/bot'),
    });
  }
}
```

まず`@aws-cdk/aws-lambda`をインポートして、`DockerImageFunction`というConstructライブラリを使ってLambdaを定義してみました。このConstructライブラリは基本的には３つの引数を必要とします。

- scope
  - 最初の引数で、そのままの意味でスコープです。慣習的に`this`を指定します。
- id
  - ２つめの引数で、リソースに与えるロジカルIDです。CloudFormation内でリソースを一意に識別するために用います。
- props
  - 最後に与える引数で、省略可能な場合もあります。Objectの形式でリソースのパラメータ値を定義します。

ここで用いた`DockerImageFunction`はコンテナ化したアプリケーション（`lambda/bot`化で実装したコード）をLambdaにデプロイさせるためのライブラリです。`props`に与えたObjectの中に`code`フィールドを指定します。これはコンテナ化してLambdaにデプロイさせるアプリケーションのディレクトリを意味します。他にも指定できるパラメータがありますが、省略した場合はConstructライブラリが決めたデフォルト値が指定されます。
また今回はLambdaに２つの環境変数を与える必要がありました（`CHANNEL_ACCESS_TOKEN`と`CHANNEL_SECRET`）。環境変数を設定するために`environmente`フィールドを指定します。

```typescript:lib/cdk-bot-handson-stack.ts
import * as cdk from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';

export class CdkBotHandsonStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // The code that defines your stack goes here
    new lambda.DockerImageFunction(this, 'bot', {
      code: lambda.DockerImageCode.fromImageAsset('./lambda/bot'),
      environment: {
        CHANNEL_ACCESS_TOKEN: '<アクセストークン>',
        CHANNEL_SECRET: '<チャンネルシークレット>',
      },
    });
  }
}
```

`<アクセストークン>`と`<チャンネルシークレット>`に`事前準備`の章で取得したものを書き込みます。