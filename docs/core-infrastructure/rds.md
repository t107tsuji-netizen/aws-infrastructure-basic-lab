# 1.RDSとは？
RDSとは Amazon Relational Database Service の略で、AWSで使える マネージド型のデータベースサービス 。

自分でDBサーバーを構築・管理しなくても、AWS上でデータベースを作って使えるサービス。

DBのインストールやパッチ適用を自動化、DBの運用負荷を減らすという利点がある。

# 2.実際にRDSを構築してみる
## RDS用セキュリティグループを作成する
インバウンドルールを追加。

タイプ：MYSQL/Aurora<br>
プロトコル：TCP<br>
ポート：3306<br>
ソース：private-linux-sg<br>

RDSには 内部LinuxサーバーからだけMySQL接続を許可する



## サブネットグループを作成する
次に、RDSを配置するサブネットのグループを作成する。

RDS<br>
↓<br>
サブネットグループ<br>
↓<br>
DBサブネットグループを作成<br>

設定例<br>
名前：my-rds-subnet-group<br>
説明：Subnet group for RDS<br>
VPC：my-vpc-01<br>

サブネットは、プライベートサブネットを選ぶ。

private-subnet-1a<br>
private-subnet-1c

RDSのDBサブネットグループは、通常 少なくとも2つのアベイラビリティゾーンのサブネット を含める必要がある


## RDS DBインスタンスを作成する
AWSコンソール<br>
↓<br>
RDS<br>
↓<br>
データベース<br>
↓<br>
データベースの作成<br>
↓<br>
フル作成<br>
↓<br>
エンジン：MySQL<br>

今回はMySQLで進める。

エンジンのタイプ：MySQLDB識別子を入力<br>
DBインスタンス識別子：my-mysql-db

マスターユーザー名とパスワードを設定<br>
マスターユーザー名：admin<br>
パスワード：自分で決める<br>

接続設定<br>
VPC：my-vpc<br>
DBサブネットグループ：my-rds-subnet-group<br>
パブリックアクセス：なし<br>
VPCセキュリティグループ：rds-mysql-sg<br>
パブリックアクセスは「なし」<br>

データベース認証<br>
パスワード認証<br>

内部サーバーをプライベートサブネットのままにして、NAT Gateway経由でインターネットへ出し、dnf install できるようにする



## Elastic IPを作成する

NAT Gatewayには、通常 Elastic IP が必要

EC2<br>
↓<br>
Elastic IP<br>
↓<br>
Elastic IP アドレスを割り当てる

設定は基本デフォルト

ネットワークボーダーグループ：現在のリージョン<br>

作成後、Elastic IPが1つできる。

例：3.xxx.xxx.xxx

## NAT Gatewayを作成する
AWSコンソール<br>
↓<br>
VPC<br>
↓<br>
NAT ゲートウェイ<br>
↓<br>
NAT ゲートウェイを作成<br>


my-nat-gateway<br>
サブネット：public-subnet<br>
接続タイプ：パブリック<br>
Elastic IP：先ほど作成したElastic IP<br>


## 内部Linuxが所属するプライベートサブネットのルートテーブルを編集
VPC<br>
↓<br>
ルートテーブル<br>
↓<br>
private-rt を選択<br>
↓<br>
ルート<br>
↓<br>
ルートを編集<br>
↓<br>
ルートを追加<br>

追加するルート

送信先：0.0.0.0/0<br>
ターゲット：NAT Gateway<br>
対象：my-nat-gateway<br>

踏み台から内部LinuxへSSH<br>
ssh -i my-ec2-key.pem ec2-user@10.0.2.96<br>
インターネットへ出られるか確認<br>
curl -I https://aws.amazon.com<br>
成功でこれ　HTTP/2 200<br>


## MySQLクライアントをインストールする
sudo dnf install -y mariadb105<br>

RDSへ接続する<br>
mysql -h RDSエンドポイント -u admin -p<br>
エンドポイント：mysql-db-01.ctwyocw00jq1.ap-southeast-2.rds.amazonaws.com -P 3306

RDS接続　mysql -h mysql-db-01.ctwyocw00jq1.ap-southeast-2.rds.amazonaws.com -P 3306 -u admin -p

実行するとパスワードを聞かれる。
Enter password:

接続できると、<br>
mysql><br>
になる。

確認：SHOW DATABASES;<br>
終了：exit;
