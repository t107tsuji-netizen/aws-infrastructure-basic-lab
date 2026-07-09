# 1.SNSトピックを作成する
後で作るCloudWatch Alarmの通知用に作成

Amazon SNS<br>
↓<br>
トピック<br>
↓<br>
トピックの作成

```
タイプ：スタンダード
名前：security-lab-alarm-topic
```

FIFOではなく、スタンダードでOK

## Gmailアドレスを登録する

作成したSNSトピックを開く

SNS<br>
↓<br>
トピック<br>
↓<br>
security-lab-alarm-topic<br>
↓<br>
サブスクリプションを作成

```
プロトコル：Eメール
エンドポイント：自分のGmailアドレス
```

## Gmail側で承認する

サブスクリプションを作成すると、GmailにAWSから確認メールが届く

件名は以下
```
AWS Notification - Subscription Confirmation
```
メール本文内の
```
Confirm subscription
```
をクリック

メールは迷惑メールフォルダに行ってることあるので注意




# 2.CloudWatch Alarmの手順
## CloudWatch Alarmの役割
CloudWatch Alarmは、メトリクスを監視して、しきい値を超えたときにアラーム状態へ遷移する機能

今回は以下を作成
```
1. CPUUtilization アラーム
2. StatusCheckFailed アラーム
```

CloudWatch Alarmは、メトリクスを監視し、しきい値を超えたときに通知や自動アクションを実行できる。<br>
EC2のCPU使用率やディスク読み書きなどを監視可能


## PUUtilizationアラームを作成する
CloudWatch<br>
↓<br>
アラーム<br>
↓<br>
すべてのアラーム<br>
↓<br>
アラームの作成

メトリクスを選ぶ

メトリクスの選択<br>
↓<br>
EC2<br>
↓<br>
インスタンス別メトリクス

対象インスタンスを選ぶ
```
security-lab-bastion
```
メトリクスはこれ
```
CPUUtilization
```

EC2の CPUUtilization は、EC2インスタンスが使用しているCPU時間の割合を示すメトリクス

### CPUアラーム条件を設定する
5分平均CPU使用率が80%以上になったらアラーム
```
Metric: CPUUtilization
Statistic: Average
Period: 5 minutes
Threshold: >= 80
Datapoints to alarm: 1 of 1
```
### 通知設定
通知アクションで、先ほど作ったSNSトピックを選ぶ
```
通知の送信先：security-lab-alarm-topic
```
アラーム名を付ける
```
アラーム名：security-lab-bastion-high-cpu
説明：Alarm when bastion EC2 CPUUtilization exceeds 80 percent
```
アラームを作成


## StatusCheckFailedアラームを作成する
CloudWatch<br>
↓<br>
アラーム<br>
↓<br>
すべてのアラーム<br>
↓<br>
アラームの作成<br>

### メトリクスを選ぶ
メトリクスの選択<br>
↓<br>
EC2<br>
↓<br>
インスタンス別メトリクス

対象EC2を選ぶ
```
security-lab-bastion
```
メトリクスはこれ
```
StatusCheckFailed
```

条件を設定する
```
統計：最大
期間：1分 または 5分
条件：StatusCheckFailed >= 1
Datapoints to alarm：1 of 1
```
EC2のステータスチェックに失敗したらアラーム

### 通知設定
通知アクションで、先ほど作ったSNSトピックを選ぶ
```
通知の送信先：security-lab-alarm-topic
```

### アラーム名を付ける
```
アラーム名：security-lab-bastion-status-check-failed
説明：Alarm when bastion EC2 status check fails
```
アラームを作成


## CloudWatch Alarmの確認

作成後、以下を確認

CloudWatch<br>
↓<br>
アラーム<br>
↓<br>
すべてのアラーム

状態は以下のどれか
```
OK	正常
ALARM	しきい値を超えている
INSUFFICIENT_DATA	まだデータ不足
```
作成直後は INSUFFICIENT_DATA になることがある。

数分待つと OK になることが多い

## アラーム機能の検証
あえて閾値の低いアラームを作成する
```
アラーム名：security-lab-bastion-high-cpu-test
```
設定
```
Metric: CPUUtilization
Statistic: Average
Period: 1 minutes
Threshold: >= 1
Datapoints to alarm: 1 of 1
```
踏み台サーバーにつける

実際にアラームがメールで来ているか確認
