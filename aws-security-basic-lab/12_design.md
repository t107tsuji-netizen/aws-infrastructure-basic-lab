## 概要

このドキュメントでは、AWS Security Basic Lab の構成意図をまとめる。

本ラボでは、AWS上に構築した基本インフラ環境に対して、IAM、CloudTrail、GuardDuty、AWS Config、Security Hub、CloudWatch、Systems Manager Session Managerを利用し、ログ記録・脅威検知・設定監査・監視・安全な管理接続を検証した。

---

## 1. 全体の設計意図

今回の構成では、単にEC2やRDSなどのAWSリソースを作成するだけでなく、作成した環境を安全に運用・監査できる状態にすることを目的とした。

主に意識した点は以下である。

- EC2にアクセスキーを直接保存しない
- CloudTrailでAWS操作ログを記録する
- CloudTrailログ保存先S3バケットを公開しない
- GuardDutyで脅威検知を有効化する
- Security Hubで検出結果を集約する
- AWS Configで設定変更と準拠状態を確認する
- CloudWatch AlarmでEC2の基本監視を行う
- Systems Manager Session Managerで秘密鍵やSSH 22番に依存しない接続を検証する


---

## 2. IAM Roleの設計意図

EC2にはIAM Roleを付与し、AWS API操作用のアクセスキーをEC2内に直接保存しない構成とした。

今回利用した主なポリシーは以下である。

```text
- CloudWatchAgentServerPolicy
- AmazonSSMManagedInstanceCore

CloudWatchAgentServerPolicy は、CloudWatch AgentがEC2上のログや追加メトリクスをCloudWatchへ送信するために利用する。

AmazonSSMManagedInstanceCore は、EC2をSystems Managerの管理対象にし、Session Managerによる接続を可能にするために利用する。

---

3. CloudTrailの設計意図

CloudTrailを有効化し、AWSアカウント内で行われた操作を記録できるようにした。

CloudTrailでは、誰が、いつ、どのAWSリソースに対して、どのような操作を行ったかを確認できる。

本ラボでは、管理イベントを記録し、ログをS3バケットへ保存する構成とした。

これにより、Security Group変更、EC2作成、IAM変更、S3設定変更などの操作を後から追跡できる。

---

4. S3ログ保存先の設計意図

CloudTrailやAWS Configのログ保存先としてS3バケットを利用した。

ログ保存先のS3バケットでは、以下を意識した。

- Block Public Accessを有効化する
- 用途ごとにバケットを分ける
- 不要な公開設定を避ける

特にCloudTrailやAWS Configのログには、AWS環境の操作履歴や設定履歴が含まれるため、誤って公開されないように保護する必要がある。

AWS Configでは、既存S3バケットを指定した際に権限不足が発生したため、AWS Config用のS3バケットを新規作成した。

---

5. GuardDutyの設計意図

GuardDutyを有効化し、AWS環境に対する脅威検知を行える状態にした。

GuardDutyは、不審なAWS API操作、認証情報漏洩の疑い、EC2の不審通信などを検知するために利用できる。

本ラボでは、実際の攻撃を発生させるのではなく、サンプルFindingを生成して、検出結果の見方を確認した。

---
7. AWS Configの設計意図

AWS Configを有効化し、AWSリソースの設定変更履歴と準拠状態を確認できるようにした。

本ラボでは、以下のようなConfig Rulesを利用した。

- restricted-ssh
- s3-bucket-public-read-prohibited
- s3-bucket-public-write-prohibited
- cloudtrail-enabled

これにより、SSH 22番が広く公開されていないか、S3バケットが公開されていないか、CloudTrailが有効かを確認できる。

AWS Configを利用することで、設定ミスや危険な変更を検出できる構成とした。

---

6. Security Hubの設計意図

Security Hubを有効化し、AWS環境のセキュリティ検出結果を集約できるようにした。

Security Hubでは、GuardDutyなどのFindingを一元的に確認できる。

また、AWS Foundational Security Best Practicesを利用し、AWS環境が基本的なセキュリティ標準に沿っているかを確認した。

これにより、個別サービスごとに確認するのではなく、セキュリティ状態をまとめて把握できる構成とした。

---


8. CloudWatch Alarmの設計意図

CloudWatch Alarmを利用し、EC2の基本的な状態監視を行った。

本ラボでは、以下のメトリクスを対象にアラームを作成した。

- CPUUtilization
- StatusCheckFailed

CPU使用率やステータスチェック失敗を監視することで、EC2の高負荷や異常状態を検知できる。
