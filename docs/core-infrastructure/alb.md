# 1.AWS上でWebサーバを2台作成する
## Webサーバー用セキュリティグループを作る
EC2<br>
↓<br>
セキュリティグループ<br>
↓<br>
セキュリティグループを作成<br>

```
名前：web-sg<br>
説明：Allow SSH and HTTP<br>
VPC：my-vpc-01<br>
```

インバウンドルールは以下
```
SSH	22	マイIP	EC2へ接続するため<br>
HTTP	80	0.0.0.0/0	ブラウザからWebページを見るため<br>
```

## EC2を作成する
サブネットはネット接続あるやつを選択<br>
SGはwebサーバー用のSG<br>

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
echo "h1>Hello AWS Web Server</h1>" | sudo tee /var/www/html/index.html
```

確認
```
cat /var/www/html/index.html
```
次のように表示されればOK
```
"h1>Hello AWS Web Server</h1>"
```

同様にもう一つのサーバーを作成

# 2.ALBを使用して負荷分散を実現する
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
プロトコル：HTTP<br>
パス：/
```
## ターゲットを登録する
ターゲットグループ作成中、または作成後に、EC2を登録
```
web-server-1<br>
web-server-2<br>
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

表示が、

Web Server 1になること確認
