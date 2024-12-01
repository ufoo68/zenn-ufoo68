---
title: "アプリのベースを実装"
---

# 開発環境

ここから実際にアプリを実装していきます。

- 言語：Node.js（バージョンは2024/12/01時点でLTSのv22.11.0おすすめ）
- エディタ：基本何でもいい（おすすめはvscode）

# Next.jsを導入

Next.jsとLIFFを導入するために、Create LIFF Appを使います。

https://developers.line.biz/ja/docs/liff/cli-tool-create-liff-app/

まずは手順通りに以下のコマンドを実行します。

```bash
npx @line/create-liff-app
```

適当なプロジェクト名を入力します

```bash
Welcome to the Create LIFF App
? Enter your project name: line-next-v0-demo
```

テンプレートにどれを使うかを聞かれるので`nextjs`を選びます。

```bash
? Which template do you want to use? (Use arrow keys)
❯ vanilla
  react
  vue
  svelte
  nextjs
  nuxtjs
```

言語の選択があるので、v0で生成されたコードが`TypeScript`なので今回はそっちを選びます。

```bash
? JavaScript or TypeScript? (Use arrow keys)
❯ JavaScript
  TypeScript
```

LIFF IDを聞かれるので、ミニアプリ作成時に記録したLIFF IDを入力します。

```bash
? Please enter your LIFF ID:
Don't you have LIFF ID? Check out https://developers.line.biz/ja/docs/liff/getting-started/ (liffId)
```

パッケージマネージャを聞かれますが、今回は`npm`を選びます。

```bash
? Which package manager do you want to use? (Use arrow keys)
❯ yarn
  npm
```

ここからNext.jsについての問い合わせが始まります。基本的にはデフォルトの回答で大丈夫なのですが、` Would you like to use App Router? `の部分は`No`で回答します。

```
✔ Would you like to use ESLint? … Yes
✔ Would you like to use Tailwind CSS? … Yes
✔ Would you like your code inside a `src/` directory? … No
✔ Would you like to use App Router? (recommended) … No
✔ Would you like to use Turbopack for next dev? … No
✔ Would you like to customize the import alias (@/* by default)? … No
```

# v0を導入

v0の導入の前にTailwindCSSを使うための下準備が必要となります。実際に生成されたプロジェクトの`styles/global.css`の内容を書き換えます。

```css:styles/global.css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

次に以下のような感じで事前に作ったテンプレートがあるのでそれをベースにアプリを実装していきます。

https://v0.dev/chat/xcmr9sLHEAx?b=b_sCQm9kCI5AA

v0は今のところ（2024/12/01時点では）基本的には無料で利用できるので、各自で作ったUIをベースにしても大丈夫です。実際に作ったUIを自分のアプリに組み込むためには右上の`Add to Docebase`をクリックして`npx shadcn add xxxxx`という部分を右クリックコピーします。

![](https://storage.googleapis.com/zenn-user-upload/ee5e2be47dea-20241201.png)

今回実装をするNext.jsのディレクトリ直下で以下のコマンドを実行します。

```bash
npx shadcn@latest add "https://v0.dev/chat/b/b_sCQm9kCI5AA"
```

Next.jsのときのような問い合わせが始まりますが、基本的にはデフォルトの回答で大丈夫です。

```bash
√ You need to create a components.json file to add components. Proceed? ... yes
√ Which style would you like to use? » New York
√ Which color would you like to use as the base color? » Neutral
√ Would you like to use CSS variables for theming? ... no / yes
✔ Writing components.json.

It looks like you are using React 19. 
Some packages may fail to install due to peer dependency issues in npm (see https://ui.shadcn.com/react-19).

√ How would you like to proceed? » Use --force
✔ Checking registry.
  Installing dependencies.

It looks like you are using React 19.
Some packages may fail to install due to peer dependency issues in npm (see https://ui.shadcn.com/react-19).

√ How would you like to proceed? » Use --force
✔ Installing dependencies.
✔ Created 5 files:
  - components\profile-page.tsx
  - components\ui\avatar.tsx
  - components\ui\button.tsx
  - components\ui\card.tsx
  - components\ui\sheet.tsx

Please review v0-generated code before incorporating it into your project to ensure completeness and accuracy.
```

これでアプリ実装のための下準備が完了しました。