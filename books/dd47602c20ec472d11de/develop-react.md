---
title: "Reactでの開発"
---

# ReactでFigmaのコンポーネントを取り込む

UI Libraryのコンポーネントの設定画面で`Get component code`というボタンがあると思います。このボタンをクリックするとReactでコンポーネントの取り込む方法を明記してくれています。

![](https://storage.googleapis.com/zenn-user-upload/c22ec92b2174-20220210.png)

cloud9のターミナルに戻って`amplify pull`をします。`--appId`各環境によって異なるはずです。また、CLI上で色々と聞かれますが全部デフォルトで問題ないのでEnterを押していきます。

```
amplify pull --appId d2dvpcy7ec2tnh --envName dev
```

おそらく`src`ディレクトリの中に２つのフォルダが作成されたはずです。

- models: データモデルを操作するために必要
- ui-components: Figmaで作成したUIコンポーネントがコード化されている

# LIFF IDをenvファイルに書く

もう一つの下準備としてLIFF IDを`.env.production.local`に記入します。LIFFの登録の章でメモしたLIFF IDを使います。

```env:.env.production.local
REACT_APP_LIFF_ID=メモしたLIFF ID
```

`.env.production.local`は一番上のディレクトリ（`linedc-ticket-liff`フォルダの中）に保存します。

# コードを書く１

あとはコーディングをするのみです。今回は以下のコードだけをいじります。

```js:src/App.js
import { useEffect, useState } from 'react';
import './App.css';

function App() {
  const [ticket, setTicket] = useState({ user_name: '', user_qr: '' });
  useEffect(() => {
    // TODO: チケット情報の取得
  });
  const registerTicket = () => {
    //TODO:  チケット登録の実装
  }
  return (
    <div className='container'>
      {/* TODO: チケットの表示 */}
      {ticket.user_name && ticket.user_qr ?
        'ここにチケットが表示される' : ticket.user_name
          ? 'チケットが登録されました。チケット発行までしばらくお待ち下さい。' :
          <button onClick={registerTicket}>チケット登録</button>}
    </div>
  )
}

export default App;
```

まずはチケット登録のために`registerTicket`を実装していきます。

```js
import { Ticket as TicketModel } from './models';
import { DataStore } from 'aws-amplify';
import liff from '@line/liff';

function App() {
  // 省略
  const registerTicket = () => {
    liff.init({ liffId: process.env.REACT_APP_LIFF_ID }).then(async () => {
      if (!liff.isLoggedIn()) {
        await liff.login();
      }
      const { userId, displayName } = await liff.getProfile();
      await DataStore.save(
        new TicketModel({
          liff_user_id: userId,
          user_name: displayName,
        })
      );
      const tickets = await DataStore.query(TicketModel);
      const { user_name, user_qr } = tickets.find((t) => t.liff_user_id === userId);
      setTicket({ user_name, user_qr });
    });
  }
}
```

まずはこの時点でデプロイをしてみましょう。

```
amplify publish
```

アプリが問題なく動作すれば、`チケット登録`ボタンを実行すると以下の期待動作になるはずです。

1. 通常のブラウザで開いた場合はLINEのログイン画面に移動
2. 「チケットが登録されました。チケット発行までしばらくお待ち下さい。」という画面表示に切り替わる

# データモデルを登録する。

一旦Amplify Studioに戻って左メニューから`Content`を選びます。`Ticket`に何かしらのデータが登録されているはずです。

![](https://storage.googleapis.com/zenn-user-upload/c6e9859fdd93-20220210.png)

ここで、直接データをクリックしてデータの書き換えを行います。

![](https://storage.googleapis.com/zenn-user-upload/8f1fad0a8c35-20220210.png)

今回はQRの自動生成は面倒なので実装しません。[QRのススメ](https://qr.quel.jp/)でQRを生成してそのURLを直貼りします。正直なところバーコードリーダーも実装していないので、QRから読み取れる情報は何でもいいのですが、一応`liff_user_id`を入れておきましょう。

# コードを書く２

実装作業も終盤となりました。まずは`useEffect`でチケットの情報を取得する実装を書きましょう

```js
import { useEffect, useState } from 'react';
import { Ticket as TicketModel } from './models';
import { DataStore } from 'aws-amplify';
import liff from '@line/liff';

function App() {
  // 省略
  useEffect(() => {
    DataStore.query(TicketModel).then(async (tickets) => {
      liff.init({ liffId: process.env.REACT_APP_LIFF_ID }).then(async () => {
        if (!liff.isLoggedIn()) {
          await liff.login();
        }
        const { userId } = await liff.getProfile();
        const ticket = tickets.find((t) => t.liff_user_id === userId);
        if (ticket) {
          setTicket({ user_name: ticket.user_name, user_qr: ticket.user_qr });
        }
      });
    });
  });
  // 省略
}
```

さいごに`Ticket`コンポーネントを呼び出します。コード全体を以下のようになりました。

```js:src/App.js
import { Ticket } from './ui-components';
import { useEffect, useState } from 'react';
import { Ticket as TicketModel } from './models';
import { DataStore } from 'aws-amplify';
import liff from '@line/liff';
import './App.css';

function App() {
  const [ticket, setTicket] = useState({ user_name: '', user_qr: '' });
  useEffect(() => {
    DataStore.query(TicketModel).then(async (tickets) => {
      liff.init({ liffId: process.env.REACT_APP_LIFF_ID }).then(async () => {
        if (!liff.isLoggedIn()) {
          await liff.login();
        }
        const { userId } = await liff.getProfile();
        const ticket = tickets.find((t) => t.liff_user_id === userId);
        if (ticket) {
          setTicket({ user_name: ticket.user_name, user_qr: ticket.user_qr });
        }
      });
    });
  });
  const registerTicket = () => {
    liff.init({ liffId: process.env.REACT_APP_LIFF_ID }).then(async () => {
      if (!liff.isLoggedIn()) {
        await liff.login();
      }
      const { userId, displayName } = await liff.getProfile();
      await DataStore.save(
        new TicketModel({
          liff_user_id: userId,
          user_name: displayName,
        })
      );
      const tickets = await DataStore.query(TicketModel);
      const { user_name, user_qr } = tickets.find((t) => t.liff_user_id === userId);
      setTicket({ user_name, user_qr });
    });
  }
  return (
    <div className='container'>
      {ticket.user_name && ticket.user_qr ?
        <Ticket ticketProp={ticket} /> : ticket.user_name
          ? 'チケットが登録されました。チケット発行までしばらくお待ち下さい。' :
          <button onClick={registerTicket}>チケット登録</button>}
    </div>
  )
}

export default App;
```

あとはデプロイをすれば完成です。

```
amplify publish
```
