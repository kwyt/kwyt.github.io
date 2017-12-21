先日Firebase Japan User Group meet up 1 に参加しました。  
興味深い内容が多く、大枠としてはFirebaseを使った分析と、アプリケーションのアーキテクチャの話が中心でした。

今回はその中で、Firebase Predictionsを取り上げて記事にします。

### Firebase Predictions

アプリ内でのユーザーの行動を予測する機械学習サービス

#### 特徴
- Google Analytics for Firebaseで収集したデータを機械学習で解析して、ユーザー属性を分類する。
  - 100日以上過去の課金状況などをみて、課金するかの予測みたいなことも可能。
- モデルの訓練や評価にはtensorflowを使っている。
- 過去100日間のデータを使って、次の7日間のユーザー行動の予測ができる。
  - 課金のようなものだと、次の7日間で課金しそうなユーザーがわかる。
- Firebaseと連動しているので、MessegingやRemote Config, A/B Testingに活かすことが可能  
  
  
特徴から見て分かるように、Firebase Predictionsを使い倒すには、AnalyticsやA/B Testingも活用する必要があります。

#### 使ってみる
アプリにアナリティクスを追加し、予測するイベントを追加する必要があります。
iOSかAndroidになりますが、今回はAndroidを選択しました。

1. アナリティクス イベントとカスタム イベントをアプリに追加する
1. アプリでアナリティクスのデータ共有を有効にする
1. 予測を作成して評価する
1. 予測のリスク許容度を設定する
1. Remote Config で予測を使用する
1. Notifications Composer で予測を使用する
