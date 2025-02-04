---
title: "ミニアプリのコード実装"
---

# 少し解説

`create-liff-app`を使ってNext.jsのテンプレートを導入するとLIFFを使ったアプリ開発のための下準備が予め実装されています。ただし今回はline loginの機能を使ってlineのアカウント情報を書き換えたいので、`pages/_app.tsx`の内容を少し書き換えます。

```tsx:pages/_app.tsx
import "../styles/globals.css";
import type { AppProps } from "next/app";
import type { Liff } from "@line/liff";
import { useState, useEffect } from "react";

function MyApp({ Component, pageProps }: AppProps) {
  const [liffObject, setLiffObject] = useState<Liff | null>(null);
  const [liffError, setLiffError] = useState<string | null>(null);

  // Execute liff.init() when the app is initialized
  useEffect(() => {
    // to avoid `window is not defined` error
    import("@line/liff")
      .then((liff) => liff.default)
      .then((liff) => {
        console.log("LIFF init...");
        liff
          .init({ liffId: process.env.NEXT_PUBLIC_LIFF_ID! })
          .then(() => {
            console.log("LIFF init succeeded.");
            setLiffObject(liff);
          })
          .then(() => {
            console.log("LIFF login...");
            if (!liff.isLoggedIn()) {
              liff.login();
            }
          })
          .catch((error: Error) => {
            console.log("LIFF init failed.");
            setLiffError(error.toString());
          });
      });
  }, []);

  // Provide `liff` object and `liffError` object
  // to page component as property
  pageProps.liff = liffObject;
  pageProps.liffError = liffError;
  return <Component {...pageProps} />;
}

export default MyApp;
```

`process.env.NEXT_PUBLIC_LIFF_ID`を使ってLIFF IDが導入されているので、デプロイする際にはデプロイ先の環境で環境変数を設定する必要があります。
またv0で作成したUIはshadcnを使ってプロジェクトに組み込まれています。具体的には`components`のディレクトリ以下にv0で生成されたコンポーネントが入っています。

```tsx:components/profile-page.tsx
import { useState } from "react"
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar"
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card"
import { Sheet, SheetContent, SheetTrigger } from "@/components/ui/sheet"
import { Menu, MessageCircle, User, Settings, LogOut } from "lucide-react"

export function ProfilePage({
  displayName = "",
  statusMessage = "",
  pictureUrl = ""
}: {
  displayName?: string;
  statusMessage?: string;
  pictureUrl?: string;
}) {
  const [isOpen, setIsOpen] = useState(false)

  const getInitials = (name: string) => {
    return name.trim().charAt(0).toUpperCase() || '?';
  };

  return (
    <div className="flex flex-col min-h-screen">
      {/* Header */}
      <header className="flex justify-between items-center p-4 bg-primary text-primary-foreground">
        <h1 className="text-xl font-bold">メッセージアプリ</h1>
        <Sheet open={isOpen} onOpenChange={setIsOpen}>
          <SheetTrigger asChild>
            <Button variant="ghost" size="icon" className="md:hidden">
              <Menu className="h-6 w-6" />
              <span className="sr-only">メニューを開く</span>
            </Button>
          </SheetTrigger>
          <SheetContent side="left" className="w-[240px] sm:w-[300px]">
            <nav className="flex flex-col gap-4">
              <a href="#" className="flex items-center gap-2 text-lg font-semibold" onClick={() => setIsOpen(false)}>
                <User size={20} />
                プロフィール
              </a>
              <a href="#" className="flex items-center gap-2 text-lg font-semibold" onClick={() => setIsOpen(false)}>
                <MessageCircle size={20} />
                メッセージ
              </a>
              <a href="#" className="flex items-center gap-2 text-lg font-semibold" onClick={() => setIsOpen(false)}>
                <Settings size={20} />
                設定
              </a>
              <a href="#" className="flex items-center gap-2 text-lg font-semibold text-red-500" onClick={() => setIsOpen(false)}>
                <LogOut size={20} />
                ログアウト
              </a>
            </nav>
          </SheetContent>
        </Sheet>
      </header>

      {/* Main content */}
      <main className="flex-grow flex items-center justify-center p-4">
        <Card className="w-full max-w-md">
          <CardHeader className="flex flex-row items-center gap-4 pb-2">
            <Avatar className="w-20 h-20">
              <AvatarImage src={pictureUrl} alt={displayName} />
              <AvatarFallback>{getInitials(displayName)}</AvatarFallback>
            </Avatar>
            <CardTitle className="text-2xl font-bold">{displayName || "Anonymous"}</CardTitle>
          </CardHeader>
          <CardContent>
            <p className="text-muted-foreground">{statusMessage || "No status message"}</p>
          </CardContent>
        </Card>
      </main>

      {/* Footer */}
      <footer className="p-4 bg-muted text-muted-foreground text-center">
        <nav className="flex justify-center gap-4">
          <a href="#" className="hover:underline">利用規約</a>
          <a href="#" className="hover:underline">プライバシーポリシー</a>
          <a href="#" className="hover:underline">お問い合わせ</a>
        </nav>
      </footer>
    </div>
  )
}
```

# アプリ実装

ここからアプリを実装しますが、１つのファイルを書き換えるだけです。

```tsx:pages/index.tsx
import type { Liff } from "@line/liff";
import type { NextPage } from "next";
import Head from "next/head";
import { ProfilePage } from "@/components/profile-page";
import { useEffect, useState } from "react";

const Home: NextPage<{ liff: Liff | null; liffError: string | null }> = ({
  liff,
  liffError
}) => {
  const [displayName, setDisplayName] = useState<string | undefined>();
  const [statusMessage, setStatusMessage] = useState<string | undefined>();
  const [pictureUrl, setPictureUrl] = useState<string | undefined>();
  useEffect(() => {
    if (liff) {
      console.log("LIFF getProfile...");
      liff.getProfile()
        .then((profile) => {
          setDisplayName(profile.displayName);
          setStatusMessage(profile.statusMessage);
          setPictureUrl(profile.pictureUrl);
        })
        .catch((error: Error) => {
          console.log("LIFF getProfile failed.", error.toString());
        });
    }
  }, [liff]);
  return (
    <div>
      <Head>
        <title>LIFF App</title>
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <main>
        <ProfilePage displayName={displayName} statusMessage={statusMessage} pictureUrl={pictureUrl} />
      </main>
    </div>
  );
};

export default Home;
```

主なUIはv0で生成されているので、ここではLIFFを使って取得したLINEのプロフィール情報をUIに繋ぎこむ部分の実装のみでアプリケーションを作成することができます。
具体的には、`useState`というReactの状態管理APIでプロフィールの情報を格納して、`useEffect`というAPIを経由してLIFFのプロフィールを繋ぎこんでいます。
