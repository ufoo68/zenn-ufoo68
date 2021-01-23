---
title: "SSM パラメータストアでクレデンシャル情報を管理する"
---

# はじめに

前章でLINE BOTを動作させるための実装が完了しました。しかし現状の実装の問題として、Lambdaへ渡す環境変数のクレデンシャル（チャンネルシークレットとアクセストークン）がハードコーディングされている状態になっています。このクレデンシャルの管理は様々な方法があるかと思いますが、本ハンズオンではSystems Manager(以下SSM)パラメータストアを使って管理をすることにします。

# AWS Systems Manager パラメータストアとは

パラメータストアは[AWS Systems Manager](https://aws.amazon.com/jp/systems-manager/)のサービスとして提供されています。設定情報や機密情報を管理するためのストレージとして用います。本ハンズオンでは事前にSSMパラメータストアにクレデンシャル情報を保存して、それをCDK上で呼び出すような形のシステムを実装していきます。

# クレデンシャル情報の保存

AWS CLIを使用してSSMパラメータストアへクレデンシャル情報を保存します。

```bash
aws ssm put-parameter --name "channel-access-token" --type String --value "<アクセストークン>"
aws ssm put-parameter --name "channel-secret" --type String --value "<チャンネルシークレット>"
```

`<アクセストークン>`と`<チャンネルシークレット>`は同様に`事前準備`の章で取得したものを書き込みます。この`ssm put-parameter`は`--name`で指定したキー値に`--value`で指定した値を保存させるコマンドです。また、同じ`name`に値を再書き込みしたい場合は後ろに`--overwrite`を追加する必要があります。

# CDKの実装

以下のコマンドを実行してSSMパラメータストアのためのパッケージをインストールします。

```bash
npm install --save 	@aws-cdk/aws-ssm
```

インストールが完了したら追加実装をしていきます。

```typescript:lib/cdk-bot-handson-stack.ts
import * as cdk from '@aws-cdk/core';
import * as lambda from '@aws-cdk/aws-lambda';
import * as apigateway from '@aws-cdk/aws-apigateway';
import * as ssm from '@aws-cdk/aws-ssm';

export class CdkBotHandsonStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // The code that defines your stack goes here
    const channelAccessToken = ssm.StringParameter.fromStringParameterAttributes(this, 'channel-access-token', {
      parameterName: 'channel-access-token',
    }).stringValue;

    const channelSecret = ssm.StringParameter.fromStringParameterAttributes(this, 'channel-secret', {
      parameterName: 'channel-secret',
    }).stringValue;

    const bot = new lambda.DockerImageFunction(this, 'bot', {
      code: lambda.DockerImageCode.fromImageAsset('./lambda/bot'),
      environment: {
        CHANNEL_ACCESS_TOKEN: channelAccessToken,
        CHANNEL_SECRET: channelSecret,
      },
    });

    const api = new apigateway.RestApi(this, 'api');
    api.root.addMethod('POST', new apigateway.LambdaIntegration(bot));
  }
}
```

`StringParameter.fromStringParameterAttributes`のConstructライブラリを使ってAWS上に保存したクレデンシャル情報を取り出します。ここでは`props`の`parameterName`にSSMパラメータストアへ保存するときに指定した`--name`の値を入れます。この値をStringの型の値で受け取ることができるので、`DockerImageFunction`の`props`の`environment`を書き換えてSSMパラメータストアで保存した値を呼び出すように変更します。

これで実装は完了です。後は`cdk deploy`を実行して前章と同様にLINE BOTが動作すれば成功です。