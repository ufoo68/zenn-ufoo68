---
title: "Auth.jsについて"
---

# Auth.jsとは

Auth.jsは認証機能のために作られたフレームワークです。Auth.jsはNextAuth.jsという名称でもありますが、Next.js以外でも使用できるフレームワークを目指していることからAuth.jsに名称を変更したようです。

https://authjs.dev/

Auth.jsは以下の特徴があります。

- Easy
  - SNS認証や、通常のパスワード認証など様々な認証方法がビルドインされている
- Flexible
  - Next.jsやSvelteKitなど様々なフレームワークにも対応していて、MySQLやMongoDBなど様々なデータベースを用いて実装ができる
- Secure
  - JWTやcookieのためのセキュアな機能が提供されている

# なぜAuth.jsを使うのか

当然Auth.jsを使わなくてもLINEログインだけを用いてログイン機能を実装することは難しくはありません。しかしAuth.jsを使うことで以下のメリットがあります。

- 他のSNS認証も組み合わせやすい
- LINEログインのセキュリティチェックリストに準拠したアプリを実装しやすい
