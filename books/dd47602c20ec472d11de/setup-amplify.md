---
title: "AWSの開発環境作成・準備（Amplify）"
---

# 概要

**Amplify**は本日のハンズオンの主役となります。今回は２つのことを行います。

1. ウェブサイトのホスティング
2. Figmaで作成したコンポーネントの取り込み

# インストール

下記のコマンドにてAmplifyのCLIをインストールします。

```
npm install -g @aws-amplify/cli
```

# 初期設定

Amplifyのインストールが完了したら下記を実行してAmplifyの初期化をします。

```
amplify init
```

設定は以下のような感じです。JavaScriptやReactの自動判定をAmplifyが行ってくれるので基本はデフォルトで問題ないです。

```
? Enter a name for the project linedcticketliff
The following configuration will be applied:

Project information
| Name: linedcticketliff
| Environment: dev
| Default editor: Visual Studio Code
| App type: javascript
| Javascript framework: react
| Source Directory Path: src
| Distribution Directory Path: build
| Build Command: npm run-script build
| Start Command: npm run-script start

? Initialize the project with the above configuration? Yes
Using default provider  awscloudformation
```

AWS認証についての選択が次に出てきます。とりあえず`AWS profile`を選択します。

```
? Select the authentication method you want to use: 
❯ AWS profile 
  AWS access keys 
```

AWSのクレデンシャルが見つからないと次に出てきます。まずはAWSにログインせよと言われますが、今回はすでにログインしているはずなのでそのままEnterを入力します。

```
AWS access credentials can not be found.
? Setup new user Yes
Follow these steps to set up access to your AWS account:

Sign in to your AWS administrator account:
https://console.aws.amazon.com/
Press Enter to continue
```

次にリージョンの選択とIAMの作成を求められます。とりあえず今回は`ap-northeast-1`を選んで、次のIAMについてはAmplifyが自動で作成されたものをそのまま使用します（IAMの名前も自動で作成されるはずです）。
とりあえず下に長いURLが出てくるはずなのでクリックします。

```
Specify the AWS Region
? region:  ap-northeast-1
Specify the username of the new IAM user:
? user name:  amplify-3najs
Complete the user creation using the AWS console
https://console.aws.amazon.com/iam/home?region=ap-northeast-1#/usersnew?step=final&accessKey&userNames=amplify-3najs&permissionType=policies&policies=arn:aws:iam::aws:policy%2FAdministratorAccess-Amplify
Press Enter to continue
```

クリック後、AWSのIAM作成画面に遷移します。特に何も考えずそのまま右下の青ボタンを押して5まで進めて大丈夫です。

![](https://storage.googleapis.com/zenn-user-upload/dd9d17fbc3af-20220208.png)

５まで進めたのちにこのような画面となります。このとき表示される`アクセスキーID`と`シークレットアクセスキー`はこの画面でしか表示されないので必ずメモしておいてください。

![](https://storage.googleapis.com/zenn-user-upload/46a0b029b541-20220208.png)

Cloud9のターミナルに戻ってEnterを押すと下のような表示がでてきます。`accessKeyId`と`secretAccessKey`に先ほどメモしたものをコピペしてください。

```
Press Enter to continue

Enter the access key of the newly created user:
? accessKeyId:  ********************
? secretAccessKey:  ****************************************
This would update/create the AWS Profile in your local machine
? Profile Name:  default

Successfully set up the new user.

For more information on AWS Profiles, see:
https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html

? Please choose the profile you want to use default
```

# ホスティングとデプロイ

これでAmplifyの初期セットアップは完了です。次に下記コマンドを実行してホスティングをします。

```
amplify hosting add
```

下記のような質問があると思いますが、デフォルトで選択されているものを選んで大丈夫です。

```
? Select the plugin module to execute Hosting with Amplify Console (Managed hosting with custom domains, Continuous deployment)
? Choose a type Manual deployment
```

ホスティングの設定が完了したらデプロイを行います。下記コマンドを実行しましょう。

```
npm i
amplify publish
```
```
? Are you sure you want to continue? Yes
```

デプロイに成功したらAmplifyのコンソール画面で今回デプロイしたアプリケーションが出てきます(`linedcticketliff`という名前のはずです)。下記のような表示になっていれば恐らくデプロイは成功です。

![](https://storage.googleapis.com/zenn-user-upload/fff99a5d624e-20220208.png)


LINEの画面に戻り、LIFF TABからライブチケットをクリックします。
![](https://storage.googleapis.com/zenn-user-upload/b9e1e8121963-20220213.png)

遷移先にエンドポイントURLが「https://example.com」になっていますので、これをAmplifyで生成された`Domain`で更新します。
![](https://storage.googleapis.com/zenn-user-upload/72b6a7a74d50-20220213.png)

差し替え後にLIFF URLでアクセスをして、下のような画面表示になっていることを確認してください。
![](https://storage.googleapis.com/zenn-user-upload/9f3f62c09cad-20220208.jpg)
