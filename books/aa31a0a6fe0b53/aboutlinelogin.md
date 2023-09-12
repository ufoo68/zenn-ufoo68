---
title: "LINEログインについて"
---

[LINEログイン](https://developers.line.biz/ja/docs/line-login/overview/)とはLINEアプリを使ったソーシャルログインのサービスです。

# ソーシャルログインとは

ソーシャルログインはSNSのアカウントを利用してWEBサイトやサービスにログインすることができる機能のことを言います。例えば以下のようなサービスでLINEアカウントを使ったユーザーログイン機能が実装されていたりします。

https://bookwalker.jp/top/

ソーシャルログインを使うことで、サイトごとのメールアドレスとパスワードを覚えなくても、`LINEログインボタン`で簡単にログインすることができます。

# LINEログインでできること

LINEログインを使うことで、以下のような様々なプラットフォームでLINEアカウントを使ったログイン機能を実装できます。

- WEBアプリ
- Unityゲーム
- iOSアプリ
- Androidアプリ

本書ではWEBアプリでのLINEログインのみを扱います。

# LINEログインのバージョン

本書ではv2.1のLINEログインを扱います。このバージョンでは、OAuth2.0をベースとしたOpenID Connectのプロトコルがサポートされています。

https://auth0.com/jp/intro-to-iam/what-is-oauth-2

https://www.openid.or.jp/document/index.html#op-doc-openid-connect
