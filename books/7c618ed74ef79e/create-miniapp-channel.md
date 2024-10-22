---
title: "ミニアプリを作成"
---

# チャネル作成

LINE Developersからプロバイダーとミニアプリのチャネルを作成します。手順については公式ページの内容↓から、チャネルタイプを`LINEミニアプリ`を選択する形で進めます。

https://developers.line.biz/ja/docs/liff/getting-started/

# LIFF IDを確認

ミニアプリの作成で必要となるのが`LIFF ID`です。`LIFF`とはミニアプリを作成するために必要なWEBフレームワークとなるもので、今回のハンズオンもLIFFを利用してミニアプリの作成を行います。
LIFFに関する設定は、チャネルの設定ページからLIFFタブで確認できます。

![](https://storage.googleapis.com/zenn-user-upload/bb71ed65653f-20241022.png)

LIFF IDを確認するために`基本情報`の項目から`LIFF URL`という項目を探します。今回は`開発用`という項目のLIFF IDを利用します。実際のLIFF IDは、URLの中にある`https://miniapp.line.me/xxx`の`xxx`の文字列がそれに該当します。

![](https://storage.googleapis.com/zenn-user-upload/462d865673ab-20241022.png)

また、`Scope`の項目で`profile`が含まれていることを確認します。

![](https://storage.googleapis.com/zenn-user-upload/4eeda15c2b63-20241022.png)