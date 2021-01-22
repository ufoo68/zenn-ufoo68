---
title: "Lambdaを立てよう"
---

# はじめに

この章では[AWS Lambda](https://aws.amazon.com/jp/lambda/)を使ってLINE BOTのアプリケーションを実行するサーバーレスサービスを立ち上げます。

# Lambdaとは

AWSが提供するサーバーレスのサービスです。Lambdaを用いることでサーバー管理が不要になったり、コード実行を自動でスケールしてくれたりなど様々なメリットを得ることができます。Lambdaを用いたコード開発の方法はいくつかありますが、今回はコンテナ化されたアプリケーションをアップロードする方法を用いることにします。

# アプリケーションの実装

別のターミナルを開いて以下のコマンドを実行します。

```bash
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