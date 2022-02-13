---
title: "はじめに"
---

本書は[【Amplify Studio×LIFF×Figma】ローコードで電子チケットアプリを作るハンズオン](https://linedevelopercommunity.connpass.com/event/237286/)に即した内容となっております。今回は2021年に発表された[AWS Amplify Studio](https://aws.amazon.com/jp/blogs/news/aws-amplify-studio-figma-to-fullstack-react-app-with-minimal-programming/)を用いてLIFFアプリをローコードで構築していきます。

今回作るアプリのイメージは↓こんな感じです。

![](https://storage.googleapis.com/zenn-user-upload/f0744f1da4ef-20220206.jpg)

内容はシンプルになっています。作るのは電子チケットアプリです。やることは以下の３つです。

1. チケットの登録
2. チケットの発行
3. チケットの表示

今回はFigmaで簡単にアプリケーションを作ることを目的にしているので、非常に簡素なアプリを作ります。なので以下のことは本書には含めないので皆様でアプリケーションの作成をしていただく必要があります。

1. チケット表示するユーザー認証
2. チケット発行のための管理者用アプリ
3. チケットのQRコードを読むためのリーダー用アプリ

これらの開発については本書は触れないので、本格的にアプリケーションを作る場合は少なくとも上記のことを加えて実装する必要があります。ちなみに今回はLIFFアプリケーションの開発のために[React](https://ja.reactjs.org/)というUIライブラリを使用します。本書では特にReactの基礎に触れることはしませんが、現時点でAmplify StudioはReactでの開発をメインにサポートしていので、本書をきっかけにReactについて触れて頂ければと思います。