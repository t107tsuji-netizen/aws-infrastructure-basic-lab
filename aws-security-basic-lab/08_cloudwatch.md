# 1.SNSトピックを作成する

後で作るCloudWatch Alarmの通知用に作成

Amazon SNS
↓
トピック
↓
トピックの作成

設定例です。

タイプ：スタンダード
名前：security-lab-alarm-topic

FIFOではなく、スタンダードでOK

Gmailアドレスを登録する

作成したSNSトピックを開きます。

SNS
↓
トピック
↓
security-lab-alarm-topic
↓
サブスクリプションを作成

設定はこうです。

プロトコル：Eメール
エンドポイント：自分のGmailアドレス


Gmail側で承認する

サブスクリプションを作成すると、GmailにAWSから確認メールが届きます。

件名はだいたい以下のようなものです。

AWS Notification - Subscription Confirmation

メール本文内の

Confirm subscription

をクリックします。

これを押さないと、SNSのサブスクリプションが Pending confirmation のままで通知されません

メールは迷惑メールフォルダに行ってることあり




# 2.CloudWatch Alarmの手順
4-1. CloudWatch Alarmの役割

CloudWatch Alarmは、メトリクスを監視して、しきい値を超えたときにアラーム状態へ遷移する機能です。

今回のラボでは、まずEC2に対して以下を作ります。

1. CPUUtilization アラーム
2. StatusCheckFailed アラーム

CloudWatch Alarmは、メトリクスを監視し、しきい値を超えたときに通知や自動アクションを実行できます。例えばEC2のCPU使用率やディスク読み書きなどを監視できます


## PUUtilizationアラームを作成する
CloudWatch
↓
アラーム
↓
すべてのアラーム
↓
アラームの作成

メトリクスを選びます。

メトリクスの選択
↓
EC2
↓
インスタンス別メトリクス

対象インスタンスを選びます。

security-lab-bastion

メトリクスはこれです。

CPUUtilization

EC2の CPUUtilization は、EC2インスタンスが使用しているCPU時間の割合を示すメトリクスです。

CPUアラーム条件を設定する

学習用の例です。

統計：平均
期間：5分
条件：静的
しきい値：CPUUtilization >= 80

つまり、

5分平均CPU使用率が80%以上になったらアラーム

という意味です。

設定例：

Metric: CPUUtilization
Statistic: Average
Period: 5 minutes
Threshold: >= 80
Datapoints to alarm: 1 of 1

通知設定
通知アクションで、先ほど作ったSNSトピックを選びます。
通知の送信先：security-lab-alarm-topic

アラーム名を付ける
アラーム名：
security-lab-bastion-high-cpu

説明：

Alarm when bastion EC2 CPUUtilization exceeds 80 percent

作成します。

アラームを作成




## StatusCheckFailedアラームを作成する
5-1. アラーム作成画面へ進む
CloudWatch
↓
アラーム
↓
すべてのアラーム
↓
アラームの作成
5-2. メトリクスを選ぶ
メトリクスの選択
↓
EC2
↓
インスタンス別メトリクス

対象EC2を選びます。

security-lab-bastion

メトリクスはこれです。

StatusCheckFailed

AWS公式でも、EC2のステータスチェックメトリクスを使って、インスタンスのステータスチェック失敗時に通知するCloudWatch Alarmを作成できると説明されています。

5-3. 条件を設定する

設定例：

統計：最大
期間：1分 または 5分
条件：StatusCheckFailed >= 1
Datapoints to alarm：1 of 1

意味はこうです。

EC2のステータスチェックに失敗したらアラーム

通知設定
通知アクションで、先ほど作ったSNSトピックを選びます。
通知の送信先：security-lab-alarm-topic


アラーム名を付ける
アラーム名：
security-lab-bastion-status-check-failed

説明：

Alarm when bastion EC2 status check fails

作成します。

アラームを作成


loudWatch Alarmの確認

作成後、以下を確認します。

CloudWatch
↓
アラーム
↓
すべてのアラーム

状態は以下のどれかになります。

状態	意味
OK	正常
ALARM	しきい値を超えている
INSUFFICIENT_DATA	まだデータ不足

作成直後は INSUFFICIENT_DATA になることがあります。
数分待つと OK になることが多い
