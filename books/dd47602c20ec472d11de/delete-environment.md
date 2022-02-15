---
title: "環境削除"
---

# amplify環境の削除
Cloud9環境のコンソールで以下を実行します。
```
amplify delete

? Are you sure you want to continue? This CANNOT be undone. (This will delete all the environments of the project from the cloud and wipe out all the local fi
les created by Amplify CLI) (y/N) Yes
```

# Cloud9の削除
Management Console -> Cloud9 -> このイベントで作成した環境をDelete

# IAM Userの削除
Management Console -> IAM -> ユーザー -> 当イベントで作成したユーザーをDelete
`アクティブなキーが作成されてからの経過`が作業時間内である物が対象です。

# Cognito UserPoolの削除
Management Console -> Cognito -> ユーザープール -> 当イベントで作成したユーザープールをDelete
`作成時刻`が作業時間内である物が対象です。

# Lineチャンネルの削除
Line Developers Console -> ライブチケット -> チャンネルの削除

# Lineチャンネルの削除
Line Developers Console -> プロバイダー設定 -> プロバイダーの削除