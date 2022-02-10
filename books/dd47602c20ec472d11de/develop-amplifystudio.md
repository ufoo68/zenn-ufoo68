---
title: "Amplify Studioでの開発"
---

# Amplify Studioを起動しよう

Amplify StudioはAmplifyに搭載された新機能となっています。今回は２つの機能を使ってみます。

まずは、Amplifyで設定したアプリにAmplify Studioを設定します。まずは`Background environments`から`Setup Amplify Studio`をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/5f00eab60003-20220208.png)

`Amplify Studioを有効にします`を`ON`にします。

![](https://storage.googleapis.com/zenn-user-upload/a1e33c3d03f4-20220208.png)

Amplifyのコンソールに戻ると、`Studioを起動する`ができるようになっています。

![](https://storage.googleapis.com/zenn-user-upload/4deadd7f434f-20220208.png)

# Data modelを定義する

チケット管理のためのデータモデルを作成します。左メニューに`Data`という項目があるのでそれをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/c11eece8c114-20220208.png)

`Add model`でこんな感じのデータを作成します。作成したら`Save and deploy`します。

![](https://storage.googleapis.com/zenn-user-upload/0dce4a49f30b-20220208.png)

- id: これは必ず一意に付与されるものです。今回は特に使いません。
- liff_user_id: LIFFのライブラリから取得できるIDをここで管理します。
- user_name: LIFFのライブラリから取得できるLINE ユーザー名をここで管理します。
- user_qr: liff_user_idから生成したQRコードの画像URLを管理します。

# Figmaのコンポーネントを取り込もう

ここからがAmplify Studioの目玉機能です。この機能を使うことで、**Figma**というデザインツールを使って作成したコンポーネントをReactのコンポーネントとして取り込むことができます。

今回はFigmaの使い方については特に触れないので、事前にFigmaで作成したチケットのコンポーネントをここにシェアします。こちらのデザインをコピーして使っていただいてかまいません。とりあえず作ったデザインをURLで共有できる形を作る必要があります。

<https://www.figma.com/file/mNL7PaEiIgwhZetvlX1NVb/Ticket?node-id=0%3A1>

Figmaの下準備ができれば、まずは左メニューから`UI Library`を選んで`Get started`をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/de0800d54dd1-20220210.png)

URLを入力するフォームが出てくるので、URLをペーストして取り込みを開始したら下のようにデザインを取り込むことができます。とりあえず`Accept All`してしまいましょう。

![](https://storage.googleapis.com/zenn-user-upload/098b4402de9f-20220210.png)

取り込んだコンポーネントを選んで`Configure`からコンポーネントを設定していきます。

![](https://storage.googleapis.com/zenn-user-upload/5e38bb6e5900-20220210.png)

まずは`Component properties`のところで`Add prop`を選択してpropを設定します。

- Name: `ticketProp`
- Type: `Ticket`という先ほど作成したデータモデルが選択できるのでそれを選びます。

![](https://storage.googleapis.com/zenn-user-upload/a15a65ca1e7d-20220210.png)

`LINE user name`を選んで`Child properties`を設定します。

- Prop: `label`
- Type: `ticketProp.user_name`が選択できるはずです。

![](https://storage.googleapis.com/zenn-user-upload/4374c51b6657-20220210.png)

`qr_img 1`を選んで`Child properties`を設定します。

- Prop: `src`
- Type: `ticketProp.user_qr`が選択できるはずです。

![](https://storage.googleapis.com/zenn-user-upload/5ff61e0a0cad-20220210.png)

これでAmplify Studioでの作業が完了しました。これからコーディング作業に入ります。