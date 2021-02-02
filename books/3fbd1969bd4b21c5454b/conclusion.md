---
title: "さいごに"
---

本ハンズオンは以上で終了です。お疲れ様でした。今回実装していただいたコードのサンプルを[こちら](https://github.com/ufoo68/kotoshi-no-anime)で公開しております。

また、今回のハンズオンで作成したアプリケーションと、ssmに保管したクレデンシャルの削除の手順について説明します。

```bash
cdk destroy
```

上記のコマンドでアプリケーションを削除します。コマンド実行後、ターミナル上で削除の確認をされると思うので`y`入力でエンター押下します。

```bash
aws ssm delete-parameter --name "channel-access-token"
aws ssm delete-parameter --name "channel-secret"
```

次に上記のコマンドでクレデンシャルを削除します。

![](https://storage.googleapis.com/zenn-user-upload/hfa9mntjwnnl4v875q4kkpj8ku23)

最後にCloud9で立ち上げた開発環境を赤枠で囲った`Delete`を選択して削除します。