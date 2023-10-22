---
title: "VercelでNext.jsを構築する"
---

# アプリケーション登録まで

まずはVercelにログインします。

https://vercel.com/

`Add New`→`Project`で新規プロジェクトを作成します。

![](https://storage.googleapis.com/zenn-user-upload/571cee0f1473-20231022.png)

`Clone Template`の中で`Next.js`を選択します。

![](https://storage.googleapis.com/zenn-user-upload/ee5c7ca1e771-20231022.png)

`2023/10/22`時点では、最新のNext.jsバージョン13がテンプレートとして適用されます。

テンプレートを選択したら以下の手順でNext.jsを構築します。

1. `Create Git Repository`でGitHubを選択
2. `Git Scope`に開発に使うGitHubアカウントを選択
3. `Repository Name`に適当な名前を入力
4. `Create`を押下

ちなみに`Create private Git Repository`はデフォルトでチェックが入っていますが、不要であれば外してもかまいません。Next.jsが構築されるまで数分かかるので待ちます。
構築が完了すれば以下の画面が表示されるので、そのまま`Continue to Dashboard`でダッシュボード画面に移動します。

![](https://storage.googleapis.com/zenn-user-upload/d819e979bf6a-20231022.png)

# GitHubでcodespaceを開く

別タブからGitHubでVercelから作成されたレポジトリを開きます。レポジトリ画面の左上あたりに`Code`という緑のドロップダウンがあるのでクリックして、`Create codespade on main`をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/8ba8492d32a6-20231022.png)

クリック後、こんな感じのエディタが別タブで開かれると思います。

![](https://storage.googleapis.com/zenn-user-upload/124eb6852a70-20231022.png)

ちなみにデプロイされたNext.jsはVercelの`Project`タブの`Deployment`か`Domains`というところに記載されているURLで公開されています。

![](https://storage.googleapis.com/zenn-user-upload/47a2aa6b6414-20231022.png)
