# 1.AWS Configの設定
AWS Configは、AWSリソースの設定変更を記録し、ルールに基づいて準拠・非準拠を評価するサービス

例えば、以下を確認できる
```
Security GroupでSSHが0.0.0.0/0に開いていないか
S3バケットが公開されていないか
CloudTrailが有効化されているか
EC2やIAM Roleなどの設定変更履歴
```
AWS Configは、対応リソースが作成・変更・削除されたことを継続的に検出し、それらをConfiguration Itemとして記録


AWS Config<br>
↓<br>
今すぐ始める

## 記録対象を選ぶ
料金を抑えるために 、記録対象を絞る

今回のラボで記録したいリソースは以下
```
EC2 Instance
Security Group
VPC
Subnet
Route Table
IAM Role
S3 Bucket
CloudTrail Trail
```

## 記録頻度を選ぶ
連続を選択

## IAM Roleを設定する
サービスリンクロールを使う
```
AWS Config service-linked role
```

## AWS Configの配信先S3バケットを選ぶ

AWS Configの設定履歴やスナップショットの配信先としてS3を選ぶ

既存のバケットは権限がないので、新しくバケットを作成を選ぶ

```
AWS Config用バケット：security-lab-config-logs
```

次へ

## AWS Config Rulesを追加する
AWS Config<br>
↓<br>
ルール<br>
↓<br>
ルールを追加

AWS Config Rulesは、AWSリソースの設定が理想状態に合っているかを評価するために作成


今回はこの4つを追加
```
1. restricted-ssh:restricted-ssh:SSH 22番が広く公開されていないか
2. s3-bucket-public-read-prohibited:S3が公開読み取り可能になっていないか
3. s3-bucket-public-write-prohibited:S3が公開書き込み可能になっていないか
4. cloudtrail-enabled:CloudTrailが有効か
```
フィルターを解除しないと検索しても出てこないので注意


## 準拠状態を確認する
AWS Config<br>
↓<br>
ルール

状態を確認
```
COMPLIANT	ルールに準拠している
NON_COMPLIANT	ルールに違反している
NOT_APPLICABLE	評価対象がない、または該当しない
```

検証として一時的に非準拠を作る


例えば、踏み台用SGのSSHを一時的に以下に
```
SSH / TCP / 22 / 0.0.0.0/0
```
restricted-ssh が NON_COMPLIANT になることを確認。

確認後、すぐ戻す
```
SSH / TCP / 22 / マイIP
```
