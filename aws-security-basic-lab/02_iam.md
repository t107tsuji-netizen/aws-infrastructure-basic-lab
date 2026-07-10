# 1.EC2用IAM Roleを作成する

EC2を作る前に、先にIAM Roleを作っておくと、EC2作成時にそのまま指定できる

## IAM Roleを作成する
AWSコンソールで進む

IAM<br>
↓<br>
ロール<br>
↓<br>
ロールを作成<br>

設定
```
信頼されたエンティティタイプ：AWSのサービス
ユースケース：EC2
```

次へ進む

## ポリシーを付与する

今回のSecurity Basic Labでは、以下を付ける。

```
CloudWatchAgentServerPolicy
AmazonSSMManagedInstanceCore
```
CloudWatchAgentServerPolicy は、後でCloudWatch Agentを使ってメトリクスやログを送るために使う。<br>
AmazonSSMManagedInstanceCore は、Systems Manager / Session Managerを使えるようにするための基本ポリシー。

検索欄でそれぞれ検索してチェック

次へ進む

## ロール名を付ける
```
ロール名：security-lab-ec2-role
説明：IAM role for EC2 instances in AWS Security Basic Lab
```

ロールを作成

## 作成後の確認
IAM<br>
↓<br>
ロール<br>
↓<br>
security-lab-ec2-role

以下を確認
```
信頼関係：EC2
許可ポリシー - CloudWatchAgentServerPolicy
            - AmazonSSMManagedInstanceCore
```
