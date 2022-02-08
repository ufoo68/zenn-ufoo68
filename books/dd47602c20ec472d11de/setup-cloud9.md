---
title: "AWSの開発環境作成・準備（Cloud9）"
---

# 概要

本日のハンズオンではCloud9の環境を、開発作業の場所として使用します。 Cloud9は、コードの記述・実行・デバッグが行えるクラウドベースの統合開発環境（IDE）です。

# Cloud9

- Cloud9のコンソール画面にアクセス
下記のURLからCloud9のコンソール画面を開き、
<https://ap-northeast-1.console.aws.amazon.com/cloud9/home?region=ap-northeast-1>
![](https://storage.googleapis.com/zenn-user-upload/izcu5i5dd1s10ibjr99q1b7tb79q)
[Name] 欄に任意の名前を入力してください。
![](https://amplify-sns.workshop.aws/images/00_prequisites/management-console-cloud9-3.png)
Instance typeはtm5.large を選択します。小さいサイズを選択するとビルド時にメモリが不足する可能性があります。（あとは基本的にデフォルトのままで構いません）
![](https://amplify-sns.workshop.aws/images/00_prequisites/management-console-cloud9-4.png)
VPCは特別な理由が無ければdefaultのものを選択して、`Next step`ボタンを押してください。
![](https://storage.googleapis.com/zenn-user-upload/6cv3qvohhfu9i7zqqzn73mcg5pz5)
:::message alert
defaultのVPCが無い場合は下記の方法でdefaultのVPCを作成してください。
:::
:::details defaultのVPC作成方法
![](https://storage.googleapis.com/zenn-user-upload/85uj5lrqrhip1py18u302a3au71o)
![](https://storage.googleapis.com/zenn-user-upload/kxfm5zg9xamo23esgnomgojz8bct)
:::
最後に`Create environment`ボタンを押してください。しばらくするとCloud9の画面が表示されます。
![](https://storage.googleapis.com/zenn-user-upload/yknfd95ibciruzjffklblcm315lg)

# ハンズオン用のコードの取得

Cloud9のターミナル領域で下記のgit cloneコマンドを実行しハンズオン用のコードを取得してください。

```shell
cd ~/environment
git clone https://github.com/ufoo68/linedc-ticket-liff.git
```

# ディスク容量の拡張

Cloud9の初期EBSサイズは10GiBです。SAMを利用する際に10GBを超える容量のサイズが必要となるため、以下の方法にてEBSボリュームサイズを32GBに変更してください。
:::message
下記のコマンドをコピペして実行しましょう！
:::

```
cd ~/environment/linedc-ticket-liff
chmod +x resize.sh
./resize.sh
```

df -Hのコマンドを実行し、/dev/nvme0n1p1が32GiB(35GB)になっていることを確認してください。
:::message
下記のコマンドをコピペして実行しましょう！
:::

```
df -H
```

```
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.1G     0  4.1G   0% /dev
tmpfs           4.1G     0  4.1G   0% /dev/shm
tmpfs           4.1G  476k  4.1G   1% /run
tmpfs           4.1G     0  4.1G   0% /sys/fs/cgroup
/dev/nvme0n1p1   35G  9.0G   26G  27% /
tmpfs           806M     0  806M   0% /run/user/1000
```
