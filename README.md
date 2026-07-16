
# AWS Infrastructure Basic Lab

## 概要

本リポジトリは、AWS上に基本的なインフラ環境を構築し、ネットワーク、サーバー、データベース、ロードバランサー、共有ストレージ、監視、セキュリティ検証を学習した記録である。

VPC、EC2、RDS、ALB、EFSなどの基本的なAWSインフラ構成に加えて、CloudTrail、GuardDuty、Security Hub、AWS Config、CloudWatch Alarmを利用したセキュリティ監査・検知・監視の仕組みを構築した。

単にAWSリソースを作成するだけではなく、以下の内容を意識してドキュメント化している。

- 構築手順
- ネットワーク設計
- セキュリティグループ設計
- 動作確認方法
- セキュリティ確認
- トラブルシューティング
- 設計意図
- 学んだこと

---

## 目的

本ラボの目的は、AWSインフラの基本構成を実際に構築し、以下を理解することである。

- Public SubnetとPrivate Subnetを分けたネットワーク設計
- 踏み台EC2を経由した内部EC2へのSSH接続
- Security Groupによる通信制御
- RDSをPrivate Subnet側に配置する構成
- ALBとTarget GroupによるWebアクセスの分散
- EFSによる複数EC2間のファイル共有
- AMIを利用したEC2の複製
- CloudTrailによるAWS操作ログの記録
- GuardDutyによる脅威検知
- Security Hubによるセキュリティ検出結果の集約
- AWS Configによるリソース設定の記録と準拠評価
- CloudWatch AlarmによるEC2監視
- AWSリソース構築時に発生した問題の切り分け

---

## 使用したAWSサービス

| サービス | 用途 |
|---|---|
| VPC | AWS上の仮想ネットワーク |
| Subnet | Public SubnetとPrivate Subnetのネットワーク分離 |
| Route Table | サブネットごとの通信経路制御 |
| Internet Gateway | Public Subnetからインターネットへの接続 |
| NAT Gateway | Private Subnetからインターネットへのアウトバウンド通信 |
| Elastic IP | NAT Gatewayなどに割り当てる固定パブリックIPアドレス |
| EC2 | Bastionサーバー、Privateサーバー、Webサーバー |
| Security Group | EC2、RDS、EFS、ALBの通信制御 |
| IAM Role | EC2などのAWSリソースへの権限付与 |
| RDS | マネージドデータベース |
| ALB | HTTPアクセスの入口および負荷分散 |
| Target Group | ALB配下のEC2管理 |
| EFS | 複数EC2間の共有ファイルストレージ |
| AMI | EC2インスタンスの複製 |
| CloudWatch | メトリクス、ログ、アラームによる監視 |
| CloudTrail | AWS操作ログの記録 |
| S3 | CloudTrailやAWS Configのログ保存先 |
| KMS | CloudTrailログなどの暗号化 |
| GuardDuty | AWS環境内の脅威検知 |
| Security Hub | セキュリティ検出結果の集約 |
| AWS Config | リソース設定変更の記録と準拠評価 |

---

## ディレクトリ構成

```text
aws-infrastructure-basic-lab/
├── README.md
│
├── architecture/
│   ├── core-architecture.md
│   └── security-architecture.md
│
├── docs/
│   ├── aws-security-basic-lab/
│   │   ├── 01_network.md
│   │   ├── 02_iam.md
│   │   ├── 03_ec2.md
│   │   ├── 04_cloudtrail.md
│   │   ├── 05_guardduty.md
│   │   ├── 06_aws-config.md
│   │   ├── 07_securityhub.md
│   │   ├── 08_cloudwatch.md
│   │   ├── 09_learning-notes.md
│   │   ├── 10_troubleshooting.md
│   │   ├── 11_design.md
│   │   └── 12_security-checks.md
│   │
│   └── core-infrastructure/
│       ├── 01_network-ec2.md
│       ├── 02_rds.md
│       ├── 03_efs.md
│       ├── 04_ami.md
│       ├── 05_alb.md
│       └── 06_troubleshooting.md
│
└── screenshots/

```

---

## ドキュメント一覧

### アーキテクチャ

| ドキュメント | 内容 |
|---|---|
| [core-architecture.md](architecture/core-architecture.md) | VPC、サブネット、EC2、RDS、EFS、ALB、NAT Gatewayを含む基本インフラ構成図 |
| [security-architecture.md](architecture/security-architecture.md) | CloudTrail、GuardDuty、Security Hub、AWS Config、CloudWatchを含むセキュリティ構成図 |

---

### 基本インフラ構築

| ドキュメント | 内容 |
|---|---|
| [01_network-ec2.md](docs/core-infrastructure/01_network-ec2.md) | VPC、Public Subnet、Private Subnet、Route Table、Internet Gateway、踏み台EC2、内部EC2の構築 |
| [02_efs.md](docs/core-infrastructure/02_efs.md) | EFSファイルシステム、マウントターゲット、NFS通信、複数EC2間のファイル共有 |
| [03_rds.md](docs/core-infrastructure/03_rds.md) | RDS MySQL、DB Subnet Group、Security Group、内部EC2からの接続確認 |
| [04_ami.md](docs/core-infrastructure/04_ami.md) | EC2からAMIを作成し、同じ構成のEC2を複製する手順 |
| [05_alb.md](docs/core-infrastructure/05_alb.md) | 2台のWeb EC2、Apache、Target Group、ALBによる負荷分散 |
| [06_troubleshooting.md](docs/core-infrastructure/06_troubleshooting.md) | ネットワーク、SSH、EFS、RDS、ALB構築時に発生した問題と解決方法 |

---

### AWSセキュリティ検証

| ドキュメント | 内容 |
|---|---|
| [01_network.md](docs/aws-security-basic-lab/01_network.md) | セキュリティ検証環境のネットワーク構成 |
| [02_iam.md](docs/aws-security-basic-lab/02_iam.md) | IAM Role、権限付与、最小権限の考え方 |
| [03_ec2.md](docs/aws-security-basic-lab/03_ec2.md) | セキュリティ検証対象となるEC2の構築 |
| [04_cloudtrail.md](docs/aws-security-basic-lab/04_cloudtrail.md) | CloudTrail、S3、KMSを利用したAWS操作ログの記録 |
| [05_guardduty.md](docs/aws-security-basic-lab/05_guardduty.md) | GuardDutyの有効化と脅威検出結果の確認 |
| [06_aws-config.md](docs/aws-security-basic-lab/06_aws-config.md) | AWS Configによるリソース設定変更の記録とルール評価 |
| [07_securityhub.md](docs/aws-security-basic-lab/07_securityhub.md) | Security Hubによるセキュリティ検出結果の集約 |
| [08_cloudwatch.md](docs/aws-security-basic-lab/08_cloudwatch.md) | CloudWatch Agent、メトリクス、アラームの設定 |
| [09_learning-notes.md](docs/aws-security-basic-lab/09_learning-notes.md) | 各サービスの役割や構築を通して学んだ内容 |
| [10_troubleshooting.md](docs/aws-security-basic-lab/10_troubleshooting.md) | セキュリティサービス構築時に発生した問題と解決方法 |
| [11_design.md](docs/aws-security-basic-lab/11_design.md) | ネットワーク、ログ保存、監視、アクセス制御の設計意図 |
| [12_security-checks.md](docs/aws-security-basic-lab/12_security-checks.md) | 構築後に実施したセキュリティ設定の確認項目 |

---

## 実施内容

### 1. VPC・ネットワーク環境構築

AWS上にVPCを作成し、Public SubnetとPrivate Subnetに分けたネットワーク環境を構築した。

主に以下を実施した。

- VPCの作成
- Public Subnetの作成
- Private Subnetの作成
- Internet Gatewayの作成
- Internet GatewayのVPCへのアタッチ
- Public Route Tableの作成
- Private Route Tableの作成
- Public Subnetへのルートテーブル関連付け
- Private Subnetへのルートテーブル関連付け
- `0.0.0.0/0 → Internet Gateway` のルート設定
- `0.0.0.0/0 → NAT Gateway` のルート設定
- VPC内の名前解決設定確認

---

### 2. 踏み台サーバー・内部EC2構築

Public Subnetに踏み台サーバーを配置し、Private Subnetに内部Linuxサーバーを配置した。

自宅PCから内部Linuxへ直接接続せず、踏み台サーバーを経由してSSH接続する構成を構築した。

主に以下を実施した。

- 踏み台サーバー用EC2の作成
- 内部Linux用EC2の作成
- 踏み台サーバーへのパブリックIPv4アドレス割り当て
- 内部LinuxのパブリックIPv4アドレス無効化
- SSHキーペアの作成
- PowerShellからのSSH接続
- Tera TermからのSSH接続
- EC2 Instance Connectによる接続
- 踏み台サーバーから内部LinuxへのSSH接続
- Security Groupをソースに指定した通信制御
- Windows上の秘密鍵ファイル権限変更

---

### 3. Security Groupによる通信制御

各AWSリソースの用途に応じてSecurity Groupを作成し、必要な通信だけを許可した。

主に以下を実施した。

- 踏み台サーバーへのSSH通信をマイIPに限定
- 内部LinuxへのSSH通信を踏み台サーバーのSecurity Groupに限定
- Web EC2へのHTTP通信をALBのSecurity Groupに限定
- RDSへのMySQL通信を内部EC2のSecurity Groupに限定
- EFSへのNFS通信を踏み台EC2と内部EC2のSecurity Groupに限定
- ALBへのHTTP通信をインターネットから許可
- IPアドレスではなくSecurity Groupをソースに指定する構成の確認

---

### 4. EFS共有ストレージ構築

EFSファイルシステムを作成し、踏み台サーバーと内部Linuxの2台から同じファイルシステムをマウントした。

主に以下を実施した。

- EFSファイルシステムの作成
- EFS用Security Groupの作成
- NFSのTCP 2049番ポート許可
- EFSマウントターゲットの作成
- `amazon-efs-utils` のインストール
- `/mnt/efs` へのEFSマウント
- 踏み台サーバーからのファイル作成
- 内部Linuxからの共有ファイル確認
- 内部Linuxからのファイル作成
- 踏み台サーバーからの共有ファイル確認
- `df -h`、`df -hT`、`mountpoint` によるマウント確認

---

### 5. RDS MySQL構築

Private Subnet側にRDS MySQLを構築し、内部Linuxから接続できることを確認した。

主に以下を実施した。

- RDS用サブネットの作成
- 複数AZを使用したDB Subnet Groupの作成
- RDS用Security Groupの作成
- MySQLのTCP 3306番ポート許可
- RDS MySQLインスタンスの作成
- パブリックアクセスの無効化
- 内部LinuxへのMySQLクライアント導入
- RDSエンドポイントを使用した接続
- データベース作成
- テーブル作成
- データの登録と確認

---

### 6. AMIによるEC2複製

構築済みのEC2インスタンスからAMIを作成し、同じ設定を持つWebサーバーを複製した。

主に以下を実施した。

- EC2インスタンスからAMIを作成
- AMIの作成状態確認
- 作成したAMIからEC2を起動
- ApacheとHTMLファイルが複製されていることを確認
- 複製後のEC2名とHTML表示内容の変更
- AMIとEBSスナップショットの関係確認

---

### 7. ALBによる負荷分散

2台のWeb EC2を作成し、Application Load Balancerを利用してHTTPアクセスを振り分ける構成を構築した。

主に以下を実施した。

- 2つの異なるAZへのPublic Subnet作成
- 2台のWeb EC2作成
- Apache/httpdのインストール
- Webサーバーごとに異なるHTMLファイルを作成
- ALB用Security Groupの作成
- Web EC2用Security GroupのHTTPソースをALBに限定
- Target Groupの作成
- 2台のWeb EC2をTarget Groupへ登録
- ヘルスチェックの設定
- Internet-facing ALBの作成
- HTTP 80番リスナーの設定
- ALBのDNS名を使用したアクセス確認
- `Web Server 1` と `Web Server 2` の応答確認

---

### 8. CloudTrailによる操作ログ記録

AWSアカウント内で行われた操作を記録するため、CloudTrailを構築した。

主に以下を実施した。

- CloudTrail証跡の作成
- 管理イベントの記録
- 読み取りイベントと書き込みイベントの確認
- ログ保存先となるS3バケットの作成
- S3 Block Public Accessの確認
- KMSキーによるログ暗号化
- KMSエイリアスの設定
- CloudTrailイベント履歴の確認
- S3に保存されたログファイルの確認

---

### 9. GuardDutyによる脅威検知

AWS環境内の不審な動作を検知するため、GuardDutyを有効化した。

主に以下を実施した。

- GuardDutyの有効化
- 検出結果画面の確認
- 脅威レベルの確認
- Findingの内容確認
- 対象リソースやアクションの確認
- GuardDuty停止方法の確認
- 利用料金と無料トライアルの確認

---

### 10. Security Hubによる検出結果集約

複数のAWSセキュリティサービスから出力される検出結果を一元管理するため、Security Hubを有効化した。

主に以下を実施した。

- Security Hubの有効化
- セキュリティスコアの確認
- 検出結果一覧の確認
- 重大度ごとのFinding確認
- AWS Foundational Security Best Practicesの確認
- セキュリティ標準と個別コントロールの確認
- 検出結果のステータス確認

---

### 11. AWS Configによる設定記録・評価

AWSリソースの設定変更を記録し、設定がルールに準拠しているか評価するため、AWS Configを構築した。

主に以下を実施した。

- AWS Configの有効化
- 設定レコーダーの作成
- 配信チャネルの設定
- S3バケットへの設定履歴保存
- AWS Configルールの追加
- リソースの準拠・非準拠状態の確認
- リソース設定変更履歴の確認
- CloudTrailとの役割の違いを整理

---

### 12. CloudWatchによるEC2監視

EC2の状態やリソース使用率を監視するため、CloudWatch Alarmを設定した。

主に以下を実施した。

- EC2標準メトリクスの確認
- CPUUtilizationの確認
- StatusCheckFailedの確認
- CloudWatch Alarmの作成
- しきい値の設定
- アラーム状態の確認
- CloudWatch Agentのインストール
- CloudWatch Agent用IAM Roleの設定
- Agentの起動状態確認
- ログとメトリクス監視の違いを整理

---

### 13. トラブルシューティング

構築中に発生した問題について、原因を切り分けて解決方法を記録した。

主な事象は以下である。

- SSH秘密鍵の権限エラー
- EC2へのSSH接続タイムアウト
- Security GroupでプレフィックスリストとCIDRを混在させた際のエラー
- RDS用DB Subnet GroupのAZ不足
- ALBのDNS名前解決失敗
- VPCのDNSホスト名設定不足
- ALB Target Groupのヘルスチェック失敗
- EFSマウント失敗
- NFS 2049番ポートの許可漏れ
- 内部Linuxからパッケージをインストールできない問題
- CloudWatch Agentの起動・停止状態確認
- CloudTrail作成時のKMSエイリアス設定
- Security HubやGuardDutyの料金確認

---

## 学んだこと

本ラボを通して、以下を学んだ。

- VPC、Subnet、Route Table、Gatewayの関係
- Public SubnetとPrivate Subnetを分離する理由
- Public SubnetはInternet Gatewayへのルートを持つサブネットであること
- Private Subnetから外部へ通信する場合はNAT Gatewayを使用すること
- 踏み台サーバーを経由して内部サーバーへ接続する構成
- Security Groupを使用した最小限の通信許可
- Security Groupを別のSecurity Groupのソースとして指定する方法
- EC2のパブリックIPとプライベートIPの違い
- RDSをインターネットへ直接公開しない構成
- DB Subnet Groupでは複数AZのサブネットが必要であること
- ALB、リスナー、Target Group、ヘルスチェックの関係
- 複数のWebサーバーにアクセスを分散する仕組み
- EFSファイルシステムとマウントターゲットの違い
- NFSを利用した複数EC2間のファイル共有
- AMIを利用したEC2複製の仕組み
- CloudTrailがAWS API操作を記録するサービスであること
- AWS Configがリソース設定の変更履歴を記録するサービスであること
- CloudWatchがメトリクス、ログ、アラームを扱う監視サービスであること
- GuardDutyがログを分析して脅威を検知するサービスであること
- Security Hubが複数サービスの検出結果を集約するサービスであること
- S3 Block Public Accessによるログ保存先の公開防止
- KMSによるログの暗号化
- IAM RoleによるEC2への権限付与
- エラー発生時にVPC、Route Table、Security Group、名前解決、サービス状態を順番に確認する方法
- 構築手順だけでなく、設計意図や確認結果を記録する重要性

---

## 補足

本リポジトリは、AWSの学習および検証を目的とした環境であり、本番環境向けの構成ではない。

特に以下の設定は、学習・検証環境向けである。

- HTTP通信に暗号化されていない80番ポートを使用している
- ALBでHTTPSリスナーやACM証明書を設定していない
- 単一のNAT Gatewayを使用している
- EC2へのSSH接続を使用している
- 踏み台サーバーを経由した秘密鍵認証を使用している
- RDSやEFSのバックアップ設計を簡略化している



