# AWS Infrastructure Basic Lab

## 概要

このリポジトリは、AWS上に基本的なインフラ環境を構築し、ネットワーク、サーバー、データベース、ロードバランサー、共有ストレージ、監視、セキュリティ検証を学習するためのポートフォリオです。

VPC、EC2、RDS、ALB、EFSなどの基本的なAWSインフラ構成に加えて、CloudTrail、GuardDuty、Security Hub、AWS Config、CloudWatch Alarmを利用したセキュリティ監査・検知・監視の仕組みも構築しました。

---

## 目的

このラボの目的は、AWSインフラの基本構成を実際に構築し、以下を理解することです。

- Public Subnet / Private Subnet を分けたネットワーク設計
- Bastion EC2を経由したPrivate EC2へのSSH接続
- Security Groupによる通信制御
- RDSをPrivate Subnet側に配置する構成
- ALBとTarget GroupによるWebアクセスの分散
- EFSによる複数EC2間のファイル共有
- CloudTrailによるAWS操作ログの記録
- GuardDutyによる脅威検知
- Security Hubによるセキュリティ検出結果の集約
- AWS Configによるリソース設定の記録と準拠評価
- CloudWatch AlarmによるEC2監視

---

## 全体構成図

![Overall Architecture](architecture/overall-architecture.png)

---

## 使用したAWSサービス

| サービス | 用途 |
|---|---|
| VPC | AWS上の仮想ネットワーク |
| Subnet | Public / Private のネットワーク分離 |
| Internet Gateway | Public Subnetからインターネットへ接続 |
| EC2 | Bastionサーバー、Privateサーバー、Webサーバー |
| Security Group | 通信制御 |
| IAM Role | EC2へのAWS権限付与 |
| RDS | マネージドデータベース |
| ALB | HTTPアクセスの入口、負荷分散 |
| Target Group | ALB配下のEC2管理 |
| EFS | 複数EC2間の共有ファイルストレージ |
| CloudWatch Alarm | EC2のCPUやステータスチェック監視 |
| CloudTrail | AWS操作ログの記録 |
| S3 | CloudTrailログ保存先 |
| GuardDuty | 脅威検知 |
| Security Hub | セキュリティ検出結果の集約 |
| AWS Config | リソース設定変更の記録と評価 |

---

## ディレクトリ構成

```text
aws-infrastructure-basic-lab/
├ README.md
├ architecture/
│  ├ overall-architecture.png
│  ├ security/
│  │  └ security-architecture.png
│  ├ rds/
│  │  └ rds-architecture.png
│  ├ alb/
│  │  └ alb-architecture.png
│  └ efs/
│     └ efs-architecture.png
│
├ docs/
│  ├ aws-security-basic-lab/
│  │  ├ design.md
│  │  ├ build-steps.md
│  │  ├ security-checks.md
│  │  ├ troubleshooting.md
│  │  └ learning-notes.md
│  │
│  ├ core-infrastructure/
│  │  ├ network-ec2.md
│  │  ├ rds.md
│  │  ├ alb.md
│  │  └ efs.md
│
├ screenshots/
│  ├ security/
│  │  ├ cloudtrail/
│  │  ├ guardduty/
│  │  ├ securityhub/
│  │  ├ config/
│  │  └ cloudwatch/
│  │
│  ├ network-ec2/
│  │  ├ vpc/
│  │  ├ ec2/
│  │  └ security-group/
│  │
│  ├ rds/
│  ├ alb/
│  └ efs/
│
└ cleanup.md
