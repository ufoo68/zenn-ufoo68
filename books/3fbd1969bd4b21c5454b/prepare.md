---
title: "事前準備"
---

# はじめに

本ハンズオンをはじめるために事前に準備していただきたいことは以下の2つです。

- AWSアカウントの作成
  - IAMユーザーの作成は今回不要です
- [LINE Developers](https://developers.line.biz/ja/)でのMessaging APIチャネル作成
  - 手順については[こちらの公式ページ](https://developers.line.biz/ja/docs/messaging-api/getting-started)で確認できます

これらさえ準備していただければその他ソフトウェア等のインストールは不要です。本ハンズオンで主に用いる開発環境は以下です。

- [Cloud9](https://aws.amazon.com/jp/cloud9/)
  - クラウド型の総合開発環境です。詳しい導入手順については後述します
- [TypeScript](https://www.typescriptlang.org/)
  - 本ハンズオンで用いる開発言語です
- [AWS CDK](https://aws.amazon.com/jp/cdk/)
  - クラウドリソースを定義のためのフレームワークです。詳しい説明については後述します

上記以外に、その他用いるライブラリや設定ファイルについてはここでは省略しています。また本ハンズオンでの趣旨と大きく関係しない技術要素については本書では詳しい説明を行いません。予めご了承ください。

# Messaging APIのチャネル作成

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