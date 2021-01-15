---
title: "事前準備"
---

# はじめに

本ハンズオンをはじめるために事前に準備していただきたいことは以下の2つです。

- AWSアカウントの作成
  - IAMユーザーの作成は今回不要です
- [LINE Developers](https://developers.line.biz/ja/)でのアカウント作成

これらさえ準備していただければその他ソフトウェア等のインストールは不要です。本ハンズオンで主に用いる開発環境は以下です。

- [Cloud9](https://aws.amazon.com/jp/cloud9/)
  - クラウド型の総合開発環境です。詳しい導入手順については後述します
- [TypeScript](https://www.typescriptlang.org/)
  - 本ハンズオンで用いる開発言語です
- [AWS CDK](https://aws.amazon.com/jp/cdk/)
  - クラウドリソースを定義のためのフレームワークです。詳しい説明については後述します

上記以外に、その他用いるライブラリや設定ファイルについてはここでは省略しています。また本ハンズオンでの趣旨と大きく関係しない技術要素については本書では詳しい説明を行いません。予めご了承ください。

# Messaging APIのチャネル作成

本ハンズオンでは「今年のアニメ」BOTを作成します。

![](https://storage.googleapis.com/zenn-user-upload/k9vlvi9mcru42v7y6uw31bj9tjmp)

機能はとても単純で、「今年のアニメ」というメッセージを送ると今年放送されているアニメのタイトルの一覧が返ってくるLINE BOTとなっています。まず、LINE BOTを作成するためにはLINE Developersにログインをして**プロバイダ**と**チャネル**の作成が必要になります。

![](https://storage.googleapis.com/zenn-user-upload/kex4x9nwdqiekfmlona6ebiw8zz6)

LINE Developersにログインをしたら、プロバイダーの`作成`ボタンをクリックしてプロバイダーを作成します。すでにプロバイダーを作成されている方はこの手順は不要です。

![](https://storage.googleapis.com/zenn-user-upload/ac4yy0mxuavx2dqum1pje1bfcapd)

プロバイダーを作成したら`新規チャネル作成`をクリックして、Messaging APIを選択します。

![](https://storage.googleapis.com/zenn-user-upload/luuz2vdgemont4yh7c43juslh3ei)

チャネル名・説明などを入力します。

![](https://storage.googleapis.com/zenn-user-upload/yjx5e6rwo1889mitk7pdtxhj9qdv)

![](https://storage.googleapis.com/zenn-user-upload/fkkuquu0ug733ck79ctvfqihf6ll)

参考として、チャネル名と説明を`今年のアニメ`として、大業種を`旅行・エンタメ・レジャー`、小業種を`その他娯楽・エンタメ`にしました。ここの記入は自由です。必要な情報を入力した後に`作成`をクリックします。
チャネルを作成したら[公式の開発ガイドライン](https://developers.line.biz/ja/docs/messaging-api/building-bot/)に従って、以下の作業を完了させてください。

- [Webhook URLを設定する](https://developers.line.biz/ja/docs/messaging-api/building-bot/#setting-webhook-url)
  - 現時点ではURLには適当なもの(https://www.google.com/ など)を入れる形で大丈夫です
- [長期のチャネルアクセストークンの発行](https://developers.line.biz/ja/docs/messaging-api/channel-access-tokens/#long-lived-channel-access-tokens)
  - 発行したチャネルトークンは後のLINE BOT開発で使うのでメモをしてください
- チャネルシークレットの確認
  - `チャネル基本設定`のタブの`チャネルシークレット`をメモします

# Cloud9のセットアップ

最初に[AWSのメインコンソール](https://aws.amazon.com/jp/)にログインをします。ログインが完了したら[Cloud9](https://aws.amazon.com/jp/cloud9/)から`AWS Cloud9を始める`をクリックしてサービスのページを開きます。

![cloud9](https://storage.googleapis.com/zenn-user-upload/nlefahk95eq33fwqvll98sw58b1j)

`Create environment`をクリックしてCloud9の使用を開始します。まずはenvironmentの名前を決めます。名前はなんでもかまいませんが、今回は`line-cdk-handson`という名前を入力します。名前の入力が完了したら`Next step`をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/xlx9ei3a3mevn1q7flp1njn6j0px)

次のステップはすべてデフォルトの設定のままで大丈夫です。そのまま下までスクロールして`Next step`をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/u5u4t3h12288mq2kecix0orgu8dx)

![](https://storage.googleapis.com/zenn-user-upload/m6f1vnwxtlgh03cw7b4pcgdvctw9)

最後のステップにてそのまま下スクロールで`Create environment`をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/6fjuvihvhalr8o2g158x13j37uc3)

立ち上がりには数分かかる場合があります。以下のようなエディタが起動すればCloud9のセットアップは完了です。

![](https://storage.googleapis.com/zenn-user-upload/s1kt0i6z2k5c8u0kxts8rx8bc45m)