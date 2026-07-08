## 1. IAM

### 概要

IAMは、AWSリソースへのアクセス権限を管理するサービス

ユーザー、グループ、ロール、ポリシーを使って、誰がどのAWSリソースに対して、どの操作をできるかを制御する。

今回の環境では、EC2にIAM Roleを付与し、AWS API操作用のアクセスキーをEC2内に直接保存しない構成を学んだ。

### 主な役割

```text
- AWSユーザーの権限管理
- IAM Roleによる一時的な権限付与
- ポリシーによる操作権限の制御
- 最小権限の原則に基づいたアクセス管理
```

### 活用例

```text
EC2にIAM Roleを付与し、CloudWatch AgentやSystems Managerで必要な権限を与える。

アクセスキーをサーバー内に置かず、IAM Roleを利用することで、認証情報漏洩のリスクを下げる。
```


---

## 2. CloudTrail

### 概要

CloudTrailは、AWSアカウント内で行われたAPI操作を記録するサービスである。

誰が、いつ、どのAWSリソースに対して、どのような操作を行ったかを確認できる。

今回の環境では、CloudTrailの証跡を作成し、操作ログをS3バケットへ保存する構成を学んだ。

### 主な役割

```text
- AWS API操作ログの記録
- 操作履歴の監査
- 不審な操作の調査
- インシデント発生時の追跡
```

### 活用例

```text
Security Groupの変更、EC2の作成、IAM Roleの変更、S3バケット設定変更などの操作をCloudTrailで確認する。

操作ログをS3へ保存し、後から監査や調査に利用する。
```



---

## 3. GuardDuty

### 概要

GuardDutyは、AWS環境に対する脅威を検知するサービスである。

CloudTrail、VPC Flow Logs、DNSログなどを分析し、不審な操作や通信を検出する。

今回の環境では、GuardDutyを有効化し、サンプルFindingを生成して検出結果の見方を確認した。

### 主な役割

```text
- 不審なAWS API操作の検知
- 認証情報漏洩の可能性検知
- EC2の不審な通信検知
- マルウェアやC2通信の疑い検知
```

### 活用例

```text
GuardDutyのFindingを確認し、重要度、対象リソース、説明、推奨対応を確認する。

検出結果をSecurity Hubへ連携し、他のセキュリティ検出結果と合わせて一元管理する。
```


---

## 4. AWS Config

### 概要

AWS Configは、AWSリソースの設定変更を記録し、ルールに基づいて準拠状態を評価するサービスである。

今回の環境では、AWS Configを有効化し、Security GroupやS3、CloudTrailなどの設定状態を評価する構成を学んだ。

### 主な役割

```text
- AWSリソースの設定履歴を記録
- リソース設定の変更を追跡
- Config Rulesによる準拠評価
- 危険な設定変更の検知
```

### 活用例

```text
restricted-ssh を利用し、SSH 22番が 0.0.0.0/0 に公開されていないか確認する。

s3-bucket-public-read-prohibited を利用し、S3バケットが公開読み取り可能になっていないか確認する。

cloudtrail-enabled を利用し、CloudTrailが有効化されているか確認する。
```



---

## 5. Security Hub

### 概要

Security Hubは、AWS環境のセキュリティ検出結果を集約し、セキュリティ標準に基づいて評価するサービスである。

GuardDutyやAWS Configなどの検出結果をまとめて確認できる。

今回の環境では、Security Hubを有効化し、AWS Foundational Security Best Practicesを確認した。

### 主な役割

```text
- セキュリティ検出結果の集約
- AWSセキュリティ標準チェック
- GuardDutyなど他サービスとの連携
- 重要度ごとのFinding確認
```

### 活用例

```text
GuardDutyのFindingをSecurity Hubで確認する。

AWS Foundational Security Best Practicesを有効化し、AWS環境が基本的なセキュリティ標準に沿っているか確認する。

Failedになっている項目を確認し、改善対象を洗い出す。
```


---

## 6. CloudWatch

### 概要

CloudWatchは、AWSリソースやアプリケーションのメトリクス、ログ、アラームを管理する監視サービスである。

今回の環境では、CloudWatch Alarmを作成し、EC2のCPU使用率やステータスチェック失敗を監視した。

### 主な役割

```text
- メトリクス監視
- ログ収集
- アラーム作成
- リソース異常の検知
```

### 活用例

```text
EC2のCPUUtilizationを監視し、一定以上のCPU使用率になった場合にアラーム状態へ遷移させる。

StatusCheckFailedを監視し、EC2インスタンスまたは基盤側の異常を検知する。

CloudWatch LogsとCloudWatch Agentを利用し、サーバー内ログを収集する。
```


