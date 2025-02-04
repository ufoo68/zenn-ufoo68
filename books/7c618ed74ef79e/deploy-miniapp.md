---
title: "デプロイと動作確認"
---

# デプロイする

今回はVercel CLIを使ってVercelにミニアプリをデプロイします。プロジェクトのディレクトリ直下で以下のコマンドを実行するだけです。

```bash
npx vercel
```

初めて実行する場合はvercelへのログインが求められます。デプロイ実行前に問い合わせが始まりますが、デフォルトでの回答で大丈夫です。

```bash 
? Link to existing project? no
? What’s your project’s name? my-app
? In which directory is your code located? ./
Local settings detected in vercel.json:
Auto-detected Project Settings (Next.js):
- Build Command: next build
- Development Command: next dev --port $PORT
- Install Command: `yarn install`, `pnpm install`, `npm install`, or `bun install`
- Output Directory: Next.js default
? Want to modify these settings? no
```

これでデプロイが完了しました。次に環境変数を組み込みます。

```bash
npx vercel env add NEXT_PUBLIC_LIFF_ID
```

`What's the value of NEXT_PUBLIC_LIFF_ID?`の部分で実際のLIFF IDを入力して、`Add NEXT_PUBLIC_LIFF_ID to which Environments`のところでは全部の環境をスペースキーで選択しておきます。

```bash
? What's the value of NEXT_PUBLIC_LIFF_ID? <実際のLIFF ID>
? Add NEXT_PUBLIC_LIFF_ID to which Environments (select 
multiple)? Production, Preview, Development
```

環境変数の設定後にprod環境へ再デプロイをします。

```bash
npx vercel --prod
```

prod環境へデプロイしたアプリのURLは`https://{プロジェクト名：上記の例だとmy-app}.vercel.app`という形で公開されています。そのURLを`ミニアプリ作成`の部分で編集したLINE Developersのコンソールの`ウェブアプリ設定`タブの `エンドポイントURL`の開発用の部分を編集して書き換えます。

![](https://storage.googleapis.com/zenn-user-upload/81322184668d-20241201.png)

# 動作確認

LINE Developersのコンソール画面の`LIFF URL`で動作確認ができます。以下のようなUIに自分のLINEのプロフィールが表示されれば成功です。

![](https://storage.googleapis.com/zenn-user-upload/565d7bac4832-20241007.png =200x)