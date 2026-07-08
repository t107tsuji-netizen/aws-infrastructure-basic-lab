## 概要

このドキュメントでは、AWS Security Basic Lab で実施したセキュリティ検証内容をまとめる。


## 1. CloudTrailの操作ログ確認
検証項目

AWSアカウント内の操作ログがCloudTrailで記録されていることを確認する。

確認内容
CloudTrail Event historyでAWS操作ログを確認
S3バケット配下にCloudTrailログが保存されることを確認

確認対象の例は以下。

```
- EC2作成
- Security Group変更
- タグ変更
- CloudTrail作成
- S3バケット作成
```

検証結果

CloudTrail Event historyでAWS操作ログを確認できた。

また、S3バケット配下にCloudTrailログが保存されることを確認した。

## 2.GuardDuty Findingの確認
検証項目

GuardDutyを有効化し、脅威検知結果を確認できることを検証する。

確認内容

GuardDutyを有効化し、サンプルFindingを生成した。

確認した項目は以下。
```
- Finding type
- Severity
- Resource
- Description
- Recommended action
```
検証結果

GuardDutyのFinding一覧にサンプルFindingが表示されることを確認した。


## AWS Config Rulesの準拠確認
検証項目

AWS ConfigでAWSリソースの設定状態を記録し、Config Rulesで準拠状態を評価できることを確認する。

確認内容

AWS Configを有効化し、以下のConfig Rulesを追加した。

- restricted-ssh
- s3-bucket-public-read-prohibited
- s3-bucket-public-write-prohibited
- cloudtrail-enabled

また、AWS Configの配信先S3バケットとして、AWS Config用の新規S3バケットを作成した。

検証結果

AWS Config Rulesの評価結果を確認できた。


7. CloudWatch Alarmの確認
検証項目

CloudWatch AlarmでEC2の基本的な状態監視ができることを確認する。

確認内容

以下のメトリクスを対象にCloudWatch Alarmを作成した。

- CPUUtilization
- StatusCheckFailed

アラーム名、対象インスタンス、しきい値、評価期間を設定した。

検証結果

CloudWatch Alarmを作成し、EC2のCPU使用率やステータスチェック失敗を監視できることを確認し
