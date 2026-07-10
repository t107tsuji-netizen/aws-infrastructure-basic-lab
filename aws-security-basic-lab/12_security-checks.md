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


## 3.AWS Config Rulesの準拠確認

### 検証項目

AWS ConfigでAWSリソースの設定状態を記録し、Config Rulesによって準拠・非準拠の状態を検出できることを確認する。

### 確認内容

AWS Configを有効化し、以下のConfig Rulesを追加した。

- restricted-ssh
- s3-bucket-public-read-prohibited
- s3-bucket-public-write-prohibited
- cloudtrail-enabled

また、AWS Configの配信先S3バケットとして、AWS Config用の新規S3バケットを作成した。

検証では、`restricted-ssh` の動作を確認するために、Security GroupのSSH接続元を一時的に `0.0.0.0/0` に変更し、意図的に非準拠の状態を作成した。

その後、AWS Config Rulesの評価結果が `NON_COMPLIANT` になるか確認した。

検証後は、SSH接続元を `My IP` に戻し、再度準拠状態に戻ることを確認した。

### 検証結果

AWS Config Rulesによって、SSH 22番が `0.0.0.0/0` に公開された状態を非準拠として検出できることを確認した。

また、Security Groupの設定を修正することで、非準拠状態から準拠状態へ戻せることを確認した。


## 4.CloudWatch Alarmの確認

### 検証項目

CloudWatch AlarmでEC2の基本的な状態監視を行い、条件を満たした場合にアラーム状態へ遷移し、通知メールが送信されることを確認する。

### 確認内容

以下のメトリクスを対象にCloudWatch Alarmを作成した。

- CPUUtilization
- StatusCheckFailed

アラーム名、対象インスタンス、しきい値、評価期間、通知先メールアドレスを設定した。

検証では、アラーム通知の動作を確認するために、CPUUtilizationのしきい値を一時的に低く設定した。

これにより、通常時のCPU使用率でもアラーム状態へ遷移するようにし、CloudWatch Alarmから通知メールが送信されるか確認した。

検証後は、しきい値を本来の監視用途に合わせた値へ戻した。

### 検証結果

CloudWatch Alarmが設定した条件を満たした際に、アラーム状態へ遷移することを確認した。

また、アラーム状態になった際、設定した通知先にメールが送信されることを確認した。
