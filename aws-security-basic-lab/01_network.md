# 1.Public Subnetを作成する
前提として、VPC　既存のmy-vpc-01を使用する<br>
IGW　既存のmy-igwを作成する<br>
キーペア：my-EC2-Keyを使う

次にPublic Subnetを作成

VPC<br>
↓<br>
サブネット<br>
↓<br>
サブネットを作成<br>

```
VPC ID：my-vpc-01
サブネット名：security-lab-public-subnet-1a
アベイラビリティゾーン：ap-southeast-2a
IPv4 subnet CIDR block：10.0.11.0/24
```

# 2.Public用ルートテーブルを作成する
次に、Public Subnet用のルートテーブルを作成

VPC<br>
↓<br>
ルートテーブル<br>
↓<br>
ルートテーブルを作成<br>


```
名前：security-lab-public-rt01
VPC：my-vpc-01
```

Public Route TableにIGWルートを追加<br>
作成した security-lab-public-rt を選択

ルート<br>
↓<br>
ルートを編集<br>
↓<br>
ルートを追加<br>

```
送信先：0.0.0.0/0
ターゲット：Internet Gateway
対象：my-igw
```

## Public SubnetをPublic Route Tableに関連付ける

security-lab-public-rt01 を選択

サブネットの関連付け<br>
↓<br>
サブネットの関連付けを編集

Public Subnetを選択

```
security-lab-public-subnet-1a
```


関連付けを保存


# 3.Private用ルートテーブルを作成する

次にPrivate Subnet用のルートテーブルを作成

VPC<br>
↓<br>
ルートテーブル<br>
↓<br>
ルートテーブルを作成

```
名前：security-lab-private-rt01
VPC：my-vpc-01
```


## Private Route Tableのルート

Private Route Tableは、localのみ


## Private SubnetをPrivate Route Tableに関連付ける

security-lab-private-rt01 を選択

サブネットの関連付け<br>
↓<br>
サブネットの関連付けを編集

Private Subnetを選択

```
security-lab-private-subnet-1a
```

関連付けを保存

# 4.Security Groupを設計

ここからSecurity Groupを作成

```
1. security-lab-bastion-sg
2. security-lab-private-ec2-sg
```

bastion-sgを作成する

踏み台EC2用のSG

EC2<br>
↓<br>
セキュリティグループ<br>
↓<br>
セキュリティグループを作成

```
セキュリティグループ名：security-lab-bastion-sg
説明：Security group for bastion host
VPC：my-vpc-01
```
インバウンドルール
```
タイプ：SSH
プロトコル：TCP
ポート：22
ソース：マイIP
```
検証環境につき一時的に、
カスタム　0.0.0.0/0
している（全世界からＳＳＨされる）
これだとEC2 Instance Connectが使えるのですぐに閉じる


アウトバウンドルール
初期値のまま

# 5.private-ec2-sgを作成する

内部EC2用のSG

EC2<br>
↓<br>
セキュリティグループ<br>
↓<br>
セキュリティグループを作成

```
セキュリティグループ名：security-lab-private-ec2-sg
説明：Security group for private EC2
VPC：my-vpc-01
```
インバウンドルール
```
タイプ：SSH
プロトコル：TCP
ポート：22
ソース：security-lab-bastion-sg
```
アウトバウンドルール
初期値のまま
