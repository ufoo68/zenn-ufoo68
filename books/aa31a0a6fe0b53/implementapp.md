---
title: "アプリケーションを実装する"
---

# はじめに

いよいよここからNext.jsの実装を進めていきます。Next13からApp Routerという新機能が使えるようになっています。このApp RouterはNext12以前から存在するPage Routerとは開発のパラダイムが少し異なります。折角なので、このApp Routerを使ってアプリケーションの実装をしたいと思います。
そして今回作るアプリケーションは以下です。

https://next-line-handson.vercel.app/

内容は最小限のものしか実装されていませんが、こんな感じです。

1. ログインボタンからシングルサインオン画面を起動
2. シングルサインオン画面からLINEログイン実行
3. LINEログインしたセッションをJWTで管理
4. ログアウトボタンでセッションを切る

# Auth.jsを導入

今回はAuth.jsを使います。ターミナルで以下のコマンドを実行します。

```
yarn add next-auth
```

パッケージのインストール終了後、`app`配下にアプリケーションを実装していきます。まずは`app/options.ts`を用意します。

![](https://storage.googleapis.com/zenn-user-upload/68940f752d8a-20231022.png)

ここにはLINEログインの導入に関する設定が記述されます。

```ts:app/options.ts
import { NextAuthOptions } from "next-auth";
import LineProvider from "next-auth/providers/line";

export const authOptions: NextAuthOptions = {
    debug: true,
    session: { strategy: "jwt" },
    providers: [
        LineProvider({
            clientId: process.env.LINE_CLIENT_ID!,
            clientSecret: process.env.LINE_CLIENT_SECRET!
        })
    ]
}
```

実はLINEログイン部分に関する実装はこれで完了です。`LineProvider`の部分がLINEログインに関する記述で、`clientId`と`clientSecret`がLINEログインの登録時に発行されたクレデンシャルに該当します（クライアントIDとクライアントシークレット）。ここでは`process.env`という形で環境変数を使っています。
後はNext.jsとAuth.jsの連携を実装していきます。Next.jsにAuth.jsを導入するときのお決まりの作法として`app/api/auth/[...nextauth]/route.ts`を用意します。

![](https://storage.googleapis.com/zenn-user-upload/ad7d75015a93-20231022.png)

以下の内容を記述します。コードの内容はお作法みたいなものなので、特に解説はしません。

```ts:app/api/auth/[...nextauth]/route.ts
import { authOptions } from "@/app/options";
import NextAuth from "next-auth";

const handler = NextAuth(authOptions);

export { handler as GET, handler as POST }
```

ちなみに`authOptions`を外部の`options`で定義した理由は、この後のアプリケーションでの実装でも流用するからです。
次にログインボタンを実装するために`app/components/buttons.tsx`を用意します。

```tsx:app/components/buttons.tsx
"use client";

import { signIn, signOut } from "next-auth/react";

export const LoginButton = () => {
  return (
    <button onClick={() => signIn()}>
      ログイン
    </button>
  );
};

export const LogoutButton = () => {
  return (
    <button onClick={() => signOut()}>
      ログアウト
    </button>
  );
};
```

`signIn()`がNext.Authでの認証をさせるためのメソッドとなります（同様に`signOut()`は認証後のセッションを切るメソッド）。ちなみに上部に`use client`という記述がありますが、これは必要です(`Error: Event handlers cannot be passed to Client Component props.`というエラーが発生します)。
では実際にログイン・ログアウトボタンのカスタムコンポーネントをページ上に導入してみます。ルートページである`app/page.tsx`の内容を書き換えます。

```tsx:app/page.tsx
import { getServerSession } from 'next-auth';
import { LoginButton, LogoutButton } from './components/buttons'
import { authOptions } from './options';

export default async function Home() {
  const session = await getServerSession(authOptions);
  return (
    <main className="flex min-h-screen flex-col items-center justify-center">
      {session ? <LogoutButton/> : <LoginButton/>}
    </main>
  )
}
```

`getServerSession`はサーバー側でセッションを取得するためのメソッドです（App Routerは`use client`を明記しない限り、サーバーサイドでのページレンダリングが基本となる）。引数として先程の`authOptions`が流用されます。

# 環境変数を設定

今回、クレデンシャル情報を`LINE_CLIENT_ID`と`LINE_CLIENT_SECRET`の環境変数で管理することにしたので、Vercelで環境変数を設定します。また、Auth.jsを使う関係として`NEXTAUTH_SECRET`も設定する必要があります。

`NEXTAUTH_SECRET`に入力する文字列は、以下のコマンドをターミナルで実行して生成してください。

```
openssl rand -base64 32
```

1. ダッシュボードの`Settings`タブを開く
2. 左メニューから`Environment Variables`を選択
3. Key`LINE_CLIENT_ID`でクライアントIDをValueに入力
4. `Add another`をクリックしてKey`LINE_CLIENT_SECRET`でクライアントシークレットをValueに入力
5. `Add another`をクリックしてKey`NEXTAUTH_SECRET`で生成した文字列をValueに入力
6. `Save`をクリック

# デプロイする

これでアプリケーションの実装は完了です。実装したアプリケーションのデプロイを行うために、追加分をcommitしてpushします。

```
git add .
git commit -m "feat: line login page"
git push
```

GitHubとVercelが連携されているので、GitHubに新たにプッシュされた内容が自動でVercel上にビルドされデプロイされます。
