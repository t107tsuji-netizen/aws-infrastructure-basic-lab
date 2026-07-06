# 1. VPCの作成

VPCを作成

名前	my-vpc-01<br>
IPv4 CIDR	10.0.0.0/16<br>
IPv6 CIDR	なし<br>
テナンシー	デフォルト<br>

<br>

# 2.サブネットを作成
VPC ID	my-vpc-01　<br>
サブネット名	public-subnet-1a　<br>
アベイラビリティゾーン	ap-southeast-1a　<br>
IPv4 サブネット CIDR	10.0.1.0/24　<br>

10.0.1.0/24 は、VPC全体 10.0.0.0/16 の中の一部を切り出したもの。

VPC：10.0.0.0/16<br>
└ サブネット：10.0.1.0/24


<br>

# 3. インターネットゲートウェイの作成

名前	my-igw

作成後、次にVPCへアタッチ。

作成した my-igw を選択<br>
↓<br>
アクション<br>
↓<br>
VPCにアタッチ<br>
↓<br>
my-vpc-01 を選択<br>
↓<br>
アタッチ<br>

<br>


# 4. ルートテーブルの作成
AWSのルートテーブルとは、VPC内の通信を「どこへ流すか」を決めるための経路表


名前	public-rt01<br>
送信先　10.0.0.0/16    local<br>

これは VPC内の通信はVPC内で処理する という意味。新しいルートテーブルには、デフォルトでVPC内通信のための local ルートが含まれる。

## ルートテーブルにインターネットゲートウェイを関連付ける
ルートテーブルに「0.0.0.0/0 → Internet Gateway」のルートを追加する

public-rt01 を選択<br>
↓<br>
ルート<br>
↓<br>
ルートを編集<br>
↓<br>
ルートを追加<br>

追加する内容<br>
送信先	0.0.0.0/0<br>
ターゲット　Internet Gateway<br>
対象	my-igw<br>

## ルートテーブルをサブネットに関連付ける

最後に、このルートテーブルを先ほど作ったサブネットに関連付ける。<br>

public-rt01 を選択<br>
↓<br>
サブネットの関連付け<br>
↓<br>
サブネットの関連付けを編集<br>
↓<br>
public-subnet-1a にチェック<br>
↓<br>
関連付けを保存<br>

これで、public-subnet-1a はインターネットゲートウェイへの経路を持つサブネットになる。

<br>


# 5. セキュリティグループの作成
セキュリティグループは、EC2に対して どの通信を許可するか を決める設定。<br>
Linuxへログインするために、今回は SSH 22番ポート を許可する。<br>
セキュリティグループはEC2の送受信トラフィックを制御する仮想ファイアウォール。

手順<br>
AWSコンソール<br>
↓<br>
EC2<br>
↓<br>
ネットワーク & セキュリティ<br>
↓<br>
セキュリティグループ<br>
↓<br>
セキュリティグループを作成<br>

設定例<br>

セキュリティグループ名：my-linux-sg<br>
説明：SSH access for Linux EC2<br>
VPC：my-vpc-01<br>
インバウンドルール<br>
外部から自分のサーバーへ入ってくる通信を許可するルール

インバウンドルールで、SSHを許可する。<br>

タイプ：SSH<br>
プロトコル：TCP<br>
ポート範囲：22<br>
ソース：マイIP<br>

自分のIPアドレスからのSSH通信を許可<br>
0.0.0.0/0<br>
これは全世界からSSH接続を受け付ける設定なので、避ける。<br>


アウトバウンドルール
自分のサーバーから外部へ出ていく通信を許可するルール

アウトバウンドは初期設定のまま。

<br>


# 6. EC2インスタンスの作成（無料枠で利用可能なRedHat Enterprise LinuxとAmazonLinux2023を選択）

AWSコンソール<br>
↓<br>
EC2<br>
↓<br>
インスタンス<br>
↓<br>
インスタンスを起動<br>

EC2は画面上部から検索すると早い

名前を入力

まず、EC2の名前を入力。

Name：test-amazonlinux2023


AMIは、サーバーに入れるOSのイメージ。
Amazon Linux 2023


## インスタンスタイプを選択

画面上で 無料利用枠の対象 と表示されているものを選ぶ。

例：
t2.micro
t3.micro

今回はt3.microを選択


## キーペアを作成

キーペアは、SSHログインに使う鍵のこと。

キーペア<br>
↓<br>
新しいキーペアの作成<br>

設定例<br>
キーペア名：my-ec2-key<br>
キーペアのタイプ：RSA<br>
プライベートキーファイル形式：.pem<br>

作成すると、以下のようなファイルがPCにダウンロードされる。<br>

my-ec2-key.pem<br>

このファイルはログインに使うので、削除NG。<br>

## ネットワーク設定

VPC：my-vpc-01<br>
サブネット：public-subnet-1a<br>
パブリックIPの自動割り当て：有効<br>
ファイアウォール：既存のセキュリティグループを選択<br>
セキュリティグループ：my-linux-sg<br>

パブリックサブネットに置いて、パブリックIPを有効にしないと、自宅PCからSSHで接続でない。<br>

ストレージ設定<br>
初期値のまま<br>
8 GiB<br>
gp3<br>

インスタンスを起動

最後に画面右側の内容を確認して、

インスタンスを起動

を押す。

起動後、EC2の一覧で状態を確認。

インスタンスの状態：実行中<br>
ステータスチェック：2/2 のチェックに合格<br>

ここまで出れば、EC2の作成は成功。

<br>


# 7. EC2インスタンス(Linux)へのログインする
## Powershellを使い秘密鍵の権限を変更
権限範囲が広すぎるため秘密鍵の使用ができず、変更が必要

cd C:\Users\test\Downloads
keyのある場所へ移動

icacls .\my-EC2-Key.pem /grant:r "DESKTOP-xxxxx\PC_User:R"	PC_Userに読み取り権限を付ける

icacls .\my-EC2-Key.pem /remove "Users"	Usersグループの権限を削除する

icacls .\my-EC2-Key.pem /remove "Authenticated Users"	認証済みユーザー全体の権限を削除する

icacls .\my-EC2-Key.pem /remove "Everyone"	Everyone権限を削除する

icacls .\my-EC2-Key.pem	現在の権限を確認する

 
## EC2 Instance Connectで接続
EC2 Instance Connectでログイン
EC2<br>
↓<br>
インスタンス<br>
↓<br>
対象インスタンスを選択<br>
↓<br>
接続<br>
↓<br>
EC2 Instance Connect<br>
↓<br>
接続<br>


接続後、以下のような画面になれば成功。

[ec2-user@ip-10-0-1-xxx ~]$

この方法の場合ソースはマイIPだと接続できない場合がある。

ソースを0.0.0.0　にするか、TeraTermやPowershellからSSHする<br>
注意：EC2 Instance Connectで接続できない場合でも、SSHを0.0.0.0/0で恒久的に開放するのは避ける。


## TeraTermで接続
TeraTermは管理者権限で起動
my-EC2-Keyを実行する際に権限が必要なため
my-EC2-Keyの権限は広すぎたため実行できず、権限を管理者だけに変更している

Tera Termを起動。

ホスト：踏み台サーバーのパブリックIPv4<br>
サービス：SSH<br>
TCPポート：22<br>
SSHバージョン：SSH2<br>

OKを押す。

初回接続時にセキュリティ警告が出たら、接続を続行


次の認証画面で以下を設定する。<br>
ユーザ名：ec2-user<br>
認証方式：RSA/DSA/ECDSA/ED25519鍵を使う<br>
秘密鍵：C:\Users\test\Downloads\my-EC2-Key.pem<br>

その後、OK


## Powershellで接続する

同じく管理者でPowershellを実行<br>
ディレクトリを変更し<br>
cd C:\Users\test\Downloads<br>

SSHで接続<br>
ssh -i .\my-EC2-Key.pem ec2-user@踏み台サーバーのパブリックIPv4<br>


<br>

# 8.2台のLinuxサーバを構築し片方は踏み台サーバにして踏み台からもう一台のLinuxにアクセスできるようにする
同じように内部サーバーを構築する

自宅PC→踏み台サーバー→内部サーバーという順番で通信する

サブネット作成、ルートテーブル作成→ルートテーブル紐づけ→セキュリティグループ作成→インスタンス起動


内部サーバーのルートテーブルにはインターネットゲートウェイのアタッチは必要ない


内部サーバーのセキュリティグループのソースIDは、踏み台サーバーのセキュリティグループを選択する


インスタンス起動時、パブリックIDは無効にする



踏み台サーバーに接続し、そこから内部サーバーに入る

自宅PCから踏み台サーバーへSSHログインする

Powershell<br>
現在のフォルダに秘密鍵がない場合は失敗する<br>
ダウンロードに移動してから実行する<br>
cd C:\Users\test\Downloads

鍵の権限が広すぎるとSSHうまくいかない<br>
権限をまず絞る必要がある<br>
ここではPCの管理者だけに権限を与え<br>
PwoershellやTeraTermは管理者で実行する<br>



その後踏み台サーバー上で<br>
秘密鍵を展開するファイルを作成<br>
vi my-EC2-Key.pem<br>

ローカルPCにある秘密鍵の中身を貼り付ける。<br>
管理者メモ帳で開くのがおすすめ<br>
すべてのファイルを選択しているか注意<br>


踏み台サーバー内で、秘密鍵の権限を変更する。<br>
chmod 400 my-ec2-key.pem


次に、内部LinuxサーバーのプライベートIPを使ってSSHする。<br>
ssh -i my-ec2-key.pem ec2-user@内部LinuxサーバーのプライベートIPv4

ログインできると、表示が内部Linuxサーバー側に変わる。

[ec2-user@ip-10-0-2-xxx ~]$


EC2 Instance Connectで接続する場合はSGのソースを　カスタム　0.0.0.0/0にする　その際に既存のインバウンドルールの変更はできない

新しく作りなおす　既存のルールは消去する

この手順が面倒な場合はAWS Session Managerを使用する<br>
今回は検証環境につきEC2 Instance Connectで接続する際は一時的に0.0.0.0を使用
