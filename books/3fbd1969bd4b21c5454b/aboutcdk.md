---
title: "AWS CDK"
---

# はじめに

この章では本ハンズオンで用いる**AWS CDK**について簡単に説明します。

# AWS CDKとは

[AWS CDK（AWS Cloud Development Kit）](https://aws.amazon.com/jp/cdk/)はAWSのインフラを構成するためのフレームワークです。Infrastructure as Code（IaC）とも呼ばれていて、インフラの構築をコーディングで行うツールをいいます。CDKの開発はいくつかのプログラミング言語が対応していますが、本ハンズオンでは[TypeScript](https://www.typescriptlang.org/)を使用します。

# なぜコーディングでの開発をするのか

では何故に[AWSのマネジメントコンソール](https://aws.amazon.com/jp/console/)を使わずにわざわざCDKを用いた開発を行うのか、その理由について説明します。まずマネジメントコンソールを使う開発とCDKを用いた開発、どちらもできることの範囲はほぼ同じです。むしろマネジメントコンソールでないとできない作業もいくつかあります。ただ単にアプリケーションを開発してそれで終わりであればマネジメントコンソールを使うだけで十分かと思われます。しかし実際にアプリケーションを本格的に開発するとしたらそれだけで終わることはないはずです。例えばあるLINE BOTのアプリケーションを`Lambda`・`API Gateway`・`DynamoDB`を使って開発するとします。作業としてはだいたい以下のような手順だと思われます。

1. Lambdaにコードを実装する
2. DynamoDBでテーブルを作成する
3. DynamoDBのIAMロールを定義してLambdaに付与する
4. API Gatewayをリソースを定義してLambdaを呼び出せるようにする

この時点では作業量としては大した量にはなりませんが、ここから追加で要件が増えてくるとマネジメントコンソールを使った開発が面倒になってきます。

- `prod`・`dev`といったマルチステージに対応する
- セキュリティ対応のためにWAFを導入する
- アプリケーションログをS3に保存する
- システムのドキュメント化

AWSの各サービス（LambdaやAPI Gatewayなど）はそれぞれ別の開発用WEB UIが提供されています。例えばLambdaの設定を変更したい場合はLambdaのサービスのページ、API Gatewayの設定を変更したい場合はAPI Gatewayのサービスのページを開いてWEB UI上で開発をする必要があります。開発に用いるサービスが増えてくると、そのサービスに付随したもの（Lambdaに与えるIAMロールなど）も増えてきてだんだんシステムのAWSのインフラ構成の全容がつかめなくなってきます。また各サービスの細かい数値設定（Lambdaのタイムアウト値など）を知りたいときはそれぞれのページから調べる必要があり、現時点でのシステムをコピーしてマルチステージに対応したい場合はそれぞれをドキュメント化（もしくは単なるメモ）して同じようなWEB UIでの操作をする必要が生じてしまいます。メインコンソールでの開発はAWSのちょっとした開発、試作程度であれば問題ありません。しかしシステム全体の把握・拡張・複製がやりにくくなってしまい、そこそこの規模感のある開発には向いていません。
それに対してCDKを用いたコーディングでのインフラ開発はAWSすべてのインフラ構成または各サービスの設定値をすべてコードで管理することができます。すべてがコードにまとまっているのでシステムの複製も容易で、システム全体の把握も比較的楽に行なえます。IaCの開発は最初の導入時にはオーバーヘッドがかかりメインコンソールでの開発に比べてやりにくくなることも事実ですが、メンテナンスや保守などのランニングコストも減らすことができるので中長期的な開発では大きなメリットを得ることができます。

# AWS CloudFormation

[AWS CloudFormation](https://aws.amazon.com/jp/cloudformation/)について少し説明します。AWS CloudFormationはAWSのインフラを構築するためのIaCサービスです。**JSON**もしくは**YAML**の形式で**スタック**というAWSリソースの集まり（つまりはインフラの構成）をテンプレートファイルとして定義します。以下にスタックの例を示します([ユーザーガイド](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/conditions-sample-templates.html)より抜粋)。

- JSON

```json
{
  "Resources" : {
    "EC2Instance" : {
      "Type" : "AWS::EC2::Instance",
      "Properties" : {
        "ImageId" : { "Fn::FindInMap" : [ "RegionMap", { "Ref" : "AWS::Region" }, "AMI" ]},
        "InstanceType" : { "Fn::If" : [
          "CreateProdResources",
          "c1.xlarge",
          {"Fn::If" : [
            "CreateDevResources",
            "m1.large",
            "m1.small"
          ]}
        ]}
      }
    },
    
    "MountPoint" : {
      "Type" : "AWS::EC2::VolumeAttachment",
      "Properties" : {
        "InstanceId" : { "Ref" : "EC2Instance" },
        "VolumeId"  : { "Ref" : "NewVolume" },
        "Device" : "/dev/sdh"
      }
    },

    "NewVolume" : {
      "Type" : "AWS::EC2::Volume",
      "Properties" : {
        "Size" : "100",
        "AvailabilityZone" : { "Fn::GetAtt" : [ "EC2Instance", "AvailabilityZone" ]}
      }
    }
  }
}
```

- YAML

```yml
Resources:
  EC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMI]
      InstanceType: !If [CreateProdResources, c1.xlarge, !If [CreateDevResources, m1.large, m1.small]]    
  MountPoint:
    Type: "AWS::EC2::VolumeAttachment"
    Properties:
      InstanceId: !Ref EC2Instance
      VolumeId: !Ref NewVolume
      Device: /dev/sdh
  NewVolume:
    Type: "AWS::EC2::Volume"
    Properties:
      Size: 100
      AvailabilityZone: !GetAtt EC2Instance.AvailabilityZone
```

まずスタックとは各テンプレートファイル全体のことをいいます。このスタックの中に`Resources`と呼ばれるフィールドがあり、この`Resources`フィールドの中にある各要素たちがAWSのインフラを構成するリソースの集まりです。この各リソースにある`Type`フィールドが使用するAWSサービスを意味していて、`Propeties`がそのリソースの設定値となっています。他にもCloudFormationには様々な機能が提供されていますが、基本的には上記で示した形式のテンプレートファイルを記述してAWSのインフラを構成します。記述したテンプレートファイルをAWSへデプロイすることでCloudFormationが実行され、AWSのインフラが自動で構築されます。
CDKは上記で示したようなスタックをプログラミング言語で記述することができて、CloudFormationで実行できるファイル形式に変換してくれるテンプレートエンジンとして使用します。

# AWS CDKの嬉しいところ

IaCでAWSを開発するメリットとCloudFormationについての説明をしたところで、最後にCDKを使うメリットについて説明します。CDKは以下のような構成になっています。

![](https://storage.googleapis.com/zenn-user-upload/e6nm1uekm12zzrgz706s9lc9ynfr)

- App
  - スタックへのパラメータの受け渡しや複数スタックの依存関係を定義
- Stack
  - スタックとその中のインフラ構成を定義
- Construct
  - 複数もしくは単体のリソースの定義を抽象化したライブラリ

`App`と`Stack`については次章以降のハンズオンでなんとなくの理解ができるかと思われます。以下にCDKでのスタック実装の例を示します。

```typescript
export class CdkStack extends cdk.Stack {
  constructor(scope: cdk.App, id: string, props?: cdk.StackProps) {
    super(scope, id, props)

    const table = new dynamodb.Table(this, 'Table', {
      partitionKey: { name: 'key', type: dynamodb.AttributeType.STRING }
    })

    const handler = new lambda.Function(this, 'Handler', {
      runtime: lambda.Runtime.NODEJS_10_X,
      code: lambda.Code.asset('lambda'),
      handler: 'index.handler',
      environment: {
        TABLE_NAME: table.tableName
      }
    })

    table.grantReadWriteData(handler)

    new apigawteway.LambdaRestApi(this, 'Endpoint', {
      handler
    })

  }
}
```

`Construct`を使うことでスタック内のリソース定義をシンプルにわかりやすく記述することができます（上記の例では`dynamodb.Table`、`lambda.Function`、`apigawteway.LambdaRestApi`それぞれのクラスが`Construct`です）。CDKを使うメリットの一つはこの`Construct`が使えることだと思います。`Construct`は簡単に説明すると複数のリソースの集まり（例えばLambda＋API Gatewayの組み合わせなど）を共通化することができたり、AWS公式が提供するデフォルト値や便利なメソッドが設定されたリソース定義が使えたりする仕組みです。CloudFormationを用いる場合でもある程度のリソースの集まりを共通化してスタック内で使いまわしたい場面があったりしますが、その場合は独自でスクリプトを書いて外からテンプレートファイルを書き換える仕組みを自分で作成する必要があります。しかしCDKが提供する`Construct`という仕組みを利用すればリソース定義の共通化を独自スクリプトやサードパーティツールを使うことなく実現することができます。
またCDKの開発にTypeScriptを用いることで、各リソースのプロパティの型定義を利用することができます。型定義を利用することでコーディングの入力補完やリソース定義の記述ミスの検出などを行うことができます。

次章よりCDKを用いたLINE BOT開発に触れていきます。実際にCDKでの開発を体験することでCDKを使うことのメリットを体感していただければと思います。