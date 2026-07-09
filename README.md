# AWS Infrastructure Basic Lab

## 概要

このリポジトリは、AWS上に基本的なインフラ環境を構築し、ネットワーク、サーバー、データベース、ロードバランサー、共有ストレージ、監視、セキュリティ検証を学習するためのポートフォリオです。

VPC、EC2、RDS、ALB、EFSなどの基本的なAWSインフラ構成に加えて、CloudTrail、GuardDuty、Security Hub、AWS Config、CloudWatch Alarmを利用したセキュリティ監査・検知・監視の仕組みも構築しました。

---

## 目的

このラボの目的は、AWSインフラの基本構成を実際に構築し、以下を理解することです。

- Public Subnet / Private Subnet を分けたネットワーク設計
- 踏み台 EC2を経由した内部 EC2へのSSH接続
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
│  ├ core-architecture.md
│  ├ security-architecture.md
│  
├ docs/
│  ├aws-security-basic-lab.md
│  │  ├01_network.md
│  │  ├02_iam.md
│  │  ├03_ec2.md
│  │  ├04_cloudtrail.md
│  │  ├05_guardduty.md
│  │  ├06_aws-config.md
│  │  ├07_securityhub.md
│  │  ├08_cloudwatch.md
│  │  ├09_learning-notes.md
│  │  ├10_troubleshooting.md
│  │  ├11_design.md
│  │  └12_security-checks.md
│  │  
│  │  
│  │
│  └ core-infrastructure
│      ├ 01_network-ec2.md
│      ├ 02_efs.md
│      ├ 03_rds.md
│      ├ 04_ami.md
│      ├ 05_alb.md
│      ├ 06_troubleshooting.md
│      └ 07_learning-notes.md
│  
│  
└screenshots/
