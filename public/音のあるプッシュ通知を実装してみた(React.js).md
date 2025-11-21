---
title: 音のあるプッシュ通知を実装してみた(React.js)
tags:
  - JavaScript
  - TypeScript
  - React
  - NotificationsAPI
private: false
updated_at: '2021-01-29T22:20:04+09:00'
id: 9c39272a55e88aecafb2
organization_url_name: null
slide: false
ignorePublish: false
---
## はじめに
最近、個人開発でプッシュ通知を実装して、音をつけたいなと思ったので実装してみました。プッシュ通知はNotificationAPIを使っているので、残念ながらサイトを開いてないと通知されません。ServiceWorkerとweb-push使って、バックグラウンドで実装できるようにしたいと思ってます。
React使ってます。

参考：
[通知APIの使用](https://developer.mozilla.org/ja/docs/Web/API/Notifications_API/Using_the_Notifications_API)
[Web Audio API](https://developer.mozilla.org/ja/docs/Web/API/Web_Audio_API)

効果音探しに使わせていただきました↓
[フリー音楽素材魔王魂](https://maoudamashii.jokersounds.com/list/se6.html#google_vignette)

## ソースコード

説明は簡単にコメントで記述しました！

```tsx:src/App.tsx
import { useEffect, useState } from "react";

function App() {
  const [hasSound, setHasSound] = useState(true);
  useEffect(() => {
    if ("Notification" in window) {
      // 通知が許可されていたら早期リターン
      const permission = Notification.permission;
      if (permission === "denied" || permission === "granted") {
        return;
      }
      // 通知の許可を求める
      Notification.requestPermission().then(() => new Notification("テスト"));
    }
  }, []);

  const handlePushNotif = () => {
    if ("Notification" in window) {
      const notif = new Notification("こんにちは！");
      // プッシュ通知が表示された時に起きるイベント
      notif.addEventListener("show", () => {
        // 状態によって音の有無を変える
        if (hasSound) {
          // 音再生
          new Audio("./push.wav").play();
        }
      });
    }
  };

  return (
    <div>
      <button onClick={handlePushNotif}>PUSH</button>
      <button onClick={() => setHasSound((prev) => !prev)}>
        {hasSound ? "音なしにする" : "音ありにする"}
      </button>
    </div>
  );
}

export default App;

```

初回アクセス時は下の画像のようなものが出てくるので許可するとプッシュ通知が出るようになります。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/9b2cc835-f50e-d4b8-8983-4ab123419b94.png)


`creact-react-app`で作られた`App.tsx`を変えただけの簡単なコードです。
押したらプッシュ通知を出すボタンと音の有無を切り替えるボタンが表示されます。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697143/d9b04819-3e00-f8a8-4b3b-873f1c5aee23.png)

一応完成のコード公開したので実際に試したい場合はクローンしてみてください！
https://github.com/NozomuTsuruta/simple-notification-audio

## 終わりに
ここまで読んでいただきありがとうございました！少しでもお役に立てれば嬉しいです
また、「もっといい方法あるよ」だったり、「こういう面白い技術があるよ」などなど気軽にコメントやTwitterで絡んでくれたりすると嬉しいです！
いろいろな技術を触りながら日々精進していきます！
