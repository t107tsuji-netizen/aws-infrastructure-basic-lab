# 1.ALB(Application Load Balancer)とは？

ELBの種類の1つで、主にWebアプリ向けのロードバランサー<br>

ロードバランサ―とは負荷分散のために、アクセスを複数に振り分けるシステム。<br>

主な目的は、負荷分散や可用性向上  





# 2.AWS上でWebサーバを2台作成する

## Webサーバー用セキュリティグループを作る

EC2<br>

↓<br>

セキュリティグループ<br>

↓<br>

セキュリティグループを作成<br>



```

名前：web-sg

説明：Allow SSH and HTTP

VPC：my-vpc-01

```



インバウンドルールは以下

```

SSH	22	マイIP	EC2へ接続するため

HTTP	80	0.0.0.0/0	ブラウザからWebページを見るため

```



## 1台目のWebサーバーを作成する

AWSコンソールからEC2インスタンスを作成する。

```text
AWSコンソール
↓
EC2
↓
インスタンス
↓
インスタンスを起動
```

EC2インスタンスの名前を設定する。

```text
名前：web01
```

AMIを選択する。

```text
AMI：Amazon Linux 2023
```

インスタンスタイプを選択する。

```text
インスタンスタイプ：t3.micro
```

SSH接続に使用するキーペアを選択する。

```text
キーペア：my-ec2-key
```

ネットワーク設定は以下のように設定する。

```text
VPC：my-vpc-01
サブネット：public-subnet-1a
パブリックIPの自動割り当て：有効
ファイアウォール：既存のセキュリティグループを選択
セキュリティグループ：web-sg
```

ストレージ設定は初期設定のままとする。

```text
容量：8 GiB
ボリュームタイプ：gp3
```

設定内容を確認し、インスタンスを起動する。

```text
インスタンスを起動
```

EC2の一覧画面で、以下の状態になっていることを確認する。

```text
インスタンスの状態：実行中
ステータスチェック：2/2のチェックに合格
パブリックIPv4アドレス：あり
サブネット：public-subnet-1a
セキュリティグループ：web-sg
```

---

## 1台目のWebサーバーへ接続する

EC2 Instance Connectを使用する場合は、以下の順番で接続する。

```text
EC2
↓
インスタンス
↓
web01を選択
↓
TeraTermで接続
```

PowerShellから接続する場合は、秘密鍵が保存されているディレクトリへ移動する。

```powershell
cd C:\Users\test\Downloads
```

SSHコマンドを実行する。

```powershell
ssh -i .\my-ec2-key.pem ec2-user@<web01のパブリックIPv4>
```

接続後、以下のようなプロンプトが表示されれば成功である。

```text
[ec2-user@ip-10-0-1-xxx ~]$
```

---

## 1台目のWebサーバーにApacheをインストールする

Webサーバーに接続してApacheをインストールする<br>

Apacheのパッケージ名は httpd <br>

```

sudo dnf install -y httpd

```

起動

```

sudo systemctl start httpd

```

自動起動を有効化

```

sudo systemctl enable httpd

```

状態確認

```

sudo systemctl status httpd

```



HTMLファイルを配置する<br>

Apacheの公開ディレクトリの場所<br>

```

/var/www/html/

```

テスト用のHTMLを作成

```

echo "<h1>Web Server 1</h1>" | sudo tee /var/www/html/index.html

```



確認

```

cat /var/www/html/index.html

```

次のように表示されればOK

```

<h1>Web Server 1</h1>

```



## 2台目のWebサーバーを作成する

1台目と同じ手順で、2台目のEC2インスタンスを作成する。

```text
AWSコンソール
↓
EC2
↓
インスタンス
↓
インスタンスを起動
```

EC2インスタンスの名前を設定する。

```text
名前：web02
```

AMIを選択する。

```text
AMI：Amazon Linux 2023
```

インスタンスタイプを選択する。

```text
インスタンスタイプ：t3.micro
```

1台目と同じキーペアを選択する。

```text
キーペア：my-ec2-key
```

ネットワーク設定は以下のように設定する。

```text
VPC：my-vpc-01
サブネット：public-subnet-1a
パブリックIPの自動割り当て：有効
ファイアウォール：既存のセキュリティグループを選択
セキュリティグループ：web-sg
```

ストレージ設定は初期設定のままとする。

```text
容量：8 GiB
ボリュームタイプ：gp3
```

設定内容を確認し、インスタンスを起動する。

```text
インスタンスを起動
```

EC2の一覧画面で、以下の状態になっていることを確認する。

```text
インスタンスの状態：実行中
ステータスチェック：2/2のチェックに合格
パブリックIPv4アドレス：あり
サブネット：public-subnet-1a
セキュリティグループ：web-sg
```

---

## 2台目のWebサーバーへ接続する

EC2 Instance Connectを使用する場合は、以下の順番で接続する。

```text
EC2
↓
インスタンス
↓
web02を選択
↓
TeraTermで接続

```

PowerShellから接続する場合は、以下のコマンドを実行する。

```powershell
cd C:\Users\test\Downloads
```

```powershell
ssh -i .\my-ec2-key.pem ec2-user@<web02のパブリックIPv4>
```

接続後、以下のようなプロンプトが表示されれば成功である。

```text
[ec2-user@ip-10-0-1-xxx ~]$
```

---

## 2台目のWebサーバーにApacheをインストールする

Apacheをインストールする。

```bash
sudo dnf install -y httpd
```

Apacheを起動する。

```bash
sudo systemctl start httpd
```

EC2再起動後もApacheが自動的に起動するように設定する。

```bash
sudo systemctl enable httpd
```

Apacheの状態を確認する。

```bash
sudo systemctl status httpd
```

2台目のWebサーバーを判別できるように、異なるHTMLを作成する。

```bash
echo "<h1>Web Server 2</h1>" | sudo tee /var/www/html/index.html
```

作成したHTMLファイルを確認する。

```bash
cat /var/www/html/index.html
```

次のように表示されれば成功である。

```text
<h1>Web Server 2</h1>
```



# 3.ALBを使用して負荷分散を実現する

## サブネットを追加で１つ作る

ALBを作るには、別々のAZにある2つのサブネット が必要



ALB用セキュリティグループを作成

```

名前：alb-sg

VPC：my-vpc-01

インバウンドルール：

HTTP	80	0.0.0.0/0

```

## Webサーバー側のセキュリティグループを調整する

Webサーバー側のSG、web-sg を設定

HTTP 80番のソースを 0.0.0.0/0 ではなく ALBのSGに変更





## ターゲットグループを作成する

ターゲットグループは、ALBが振り分ける先のEC2グループ



EC2<br>

↓<br>

ターゲットグループ<br>

↓<br>

ターゲットグループの作成<br>



```

ターゲットタイプ：Instances

ターゲットグループ名：web-tg

プロトコル：HTTP

ポート：80

VPC：my-vpc-01

```

```

プロトコル：HTTP

パス：/

```

## ターゲットを登録する

ターゲットグループ作成中、または作成後に、EC2を登録

```

web01

web02

```

両方にチェックを入れて、ターゲットに登録



## ALBを作成する

EC2<br>

↓<br>

ロードバランサー<br>

↓<br>

ロードバランサーを作成<br>

↓<br>

Application Load Balancer<br>



```

名前：web-alb

スキーム：Internet-facing

IPアドレスタイプ：IPv4

VPC：my-vpc-01

```

サブネットは、2つのAZから選ぶ

```

ap-southeast-2a：public-subnet-2a

ap-southeast-2b：public-subnet-2b

```



セキュリティグループは、

alb-sgを指定



リスナーは、HTTP : 80



デフォルトアクションは、Forward to web-tg



## ALBのDNS名でアクセス確認する



ALB作成後、詳細画面にDNS名が表示される



```

web-alb-xxxxxxxx.ap-southeast-2.elb.amazonaws.com

```



ブラウザでアクセス

```

http://web-alb-xxxxxxxx.ap-southeast-2.elb.amazonaws.com

```



以下のいずれかが表示されることを確認する。
```
Web Server 1
```
または、
```
Web Server 2
```
