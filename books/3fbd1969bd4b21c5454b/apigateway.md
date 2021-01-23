---
title: "API Gatewayを立てよう"
---

# はじめに

前章でアプリケーションを実行するためのLambdaの実装を行いました。この章ではWebhook URLからLambdaを呼び出すためのAPI Gatewayを設定します。

# API Gatewayとは

[Amazon API Gateway](https://aws.amazon.com/jp/api-gateway/)はAPIを作成するためのフルマネージドサービスです。使い方はかなり多種多様ではあるのですが、本ハンズオンでは[Lambda Integration](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/getting-started-with-lambda-integration.html)を用いてAPI GatewayとLambdaとの連携を行います。

# CDKの実装

以下のコマンドを実行してAPI Gatewayのためのパッケージをインストールします。前章と同じくバージョンには注意する必要があります。

```bash
npm install --save 	@aws-cdk/aws-apigateway
```

Constructライブラリを用いれば実装は２行程度で行うことができます。

```typescript:lib/cdk-bot-handson-stack.ts
import * as cdk from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';
import * as apigateway from '@aws-cdk/aws-apigateway';

export class CdkBotHandsonStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // The code that defines your stack goes here
    const bot = new lambda.DockerImageFunction(this, 'bot', {
      code: lambda.DockerImageCode.fromImageAsset('./lambda/bot'),
      environment: {
        CHANNEL_ACCESS_TOKEN: '<アクセストークン>',
        CHANNEL_SECRET: '<チャンネルシークレット>',
      },
    });

    const api = new apigateway.RestApi(this, 'api');
    api.root.addMethod('POST', new apigateway.LambdaIntegration(bot));
  }
}
```

ここでは`RestApi`というConstructライブラリを用いました。`props`へ渡す引数は省略したのでデフォルト値が適用されることになります。次に`addMethod`を呼び出すことで、API GatewayのエンドポイントからLambdaを呼び出すためのメソッドを定義することができます。この実装でAPI Gatewayで作成したAPIのエンドポイントにPOSTメソッドのリクエストが叩かれたときにLambdaが呼び出されるようになりました。

# デプロイしてみよう

ここまでの実装が完了したら一度CloudFormationのテンプレートファイルへの変換を実行してみましょう（`npm run watch`を並列実行していない方は`npm run build`を一度実行してみましょう）。

```bash
cdk synth
```

おそらく`cdk.out/CdkBotHandsonStack.template.json`内でAPI GatewayとLambdaが定義されたテンプレートが作成されているはずです。テンプレートファイルが作成されたのでデプロイをしていきます。

```bash
cdk deploy
```

最初のデプロイの場合おそらくターミナル上に、新たに作成されるリソースとそれについてのデプロイの確認が表示されます。そのまま`y`を入力＋エンターでデプロイを開始します。デプロイが完了したらターミナルの最後の行に以下のような表示があるかと思います。

```bash
Outputs:
CdkBotHandsonStack.apiEndpointXXXX = https://xxxxxxx.execute-api.ap-northeast-1.amazonaws.com/prod/
```

ここで表示されているURLがAPI Gatewayのエンドポイントとなります。`事前準備`の章で適当なURLを入力したWebhook URLがあったかと思いますが、このURLと置き換えます。Webhookが設定できたらこの時点でLINE BOTが問題なく動作するはずです。