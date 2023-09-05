---
title: "Auth.jsについて"
---

# Auth.jsとは

Auth.jsは認証機能のために作られたフレームワークです。昔はNextAuth.jsという名称だったようですが、最近はNext.js以外でも使用できるようになったのでAuth.jsに名称を変更したようです。

https://authjs.dev/

Auth.jsは以下の特徴があります。

- Easy
  - SNS認証や、通常のパスワード認証など様々な認証方法がビルドインされている
- Flexible
  - Next.jsやSvelteKitなど様々なフレームワークにも対応していて、MySQLやMongoDBなど様々なデータベースを用いて実装ができる
- Secure
  - JWTやcookieのためのセキュアな機能が提供されている

# なぜAuth.jsを使うのか

当然Auth.jsを使わなくてもLINE Loginだけを用いてログイン機能を実装することは難しくはありません。しかしAuth.jsを使うことで以下のメリットがあります。

- 他のSNS認証も組み合わせやすい
- LINEログインのセキュリティチェックリストに準拠したアプリを実装しやすい

# LINEログインのセキュリティチェックリスト

LINEログインのドキュメントでは、安全なアプリの実装をするためのチェックリストが公開されています。

https://developers.line.biz/ja/docs/line-login/security-checklist

このチェックリストにはいくつかの要件が記載されていますが、今回重要になるのが`トークンの検証`です。Auth.jsを用いることでセキュアなトークン検証の実装を容易に行うことができます。
