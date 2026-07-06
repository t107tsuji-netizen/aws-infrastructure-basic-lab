# 1.EFS(ファイル共有)とは
EFSはAWSが提供するクラウドネイティブなNFS互換のフルマネージドサービス。

EFSは 複数のEC2から同時に使える共有フォルダ のようなもの。

例えば、2台のLinuxサーバーがある場合、それぞれから同じEFSをマウントすると、同じディレクトリを見られる。

# 2.EFS構築
別サーバーが必要になるので、立てておく

## EFS専用のセキュリティグループを作成
セキュリティグループ名：efs-sg<br>
説明：Allow NFS from Linux EC2<br>
VPC：my-vpc<br>

## インバウンドルールを追加
タイプ：NFS<br>
プロトコル：TCP<br>
ポート：2049<br>
ソース：EFSサーバー01のSG<br>

タイプ：NFS<br>
プロトコル：TCP<br>
ポート：2049<br>
ソース：EFSサーバー02のSG<br>
（マウントするサーバーのSGすべて）

## EFSファイルシステムを作成する
AWSコンソール<br>
↓<br>
EFS<br>
↓<br>
ファイルシステム<br>
↓<br>
ファイルシステムを作成<br>

名前：my-efs<br>
VPC：my-vpc<br>



## マウントターゲットを確認・作成する
EFSをEC2から使うには、VPC内に マウントターゲット が必要。<br>
EFSのマウントターゲットは、EC2がEFSへ接続する入口。

ここで、VPCが my-vpc-01 になっていることを確認。

マウントターゲットのセキュリティグループには、先ほど作ったものを指定。

セキュリティグループ：efs-sg


## サーバーにEFSをマウントする

まず、AWSコンソールのEC2 Instance Connectなどで踏み台サーバーにログイン。<br>
ログイン後、EFS用ツールをインストール。<br>

Amazon Linux 2023で次を実行。

sudo dnf install -y amazon-efs-utils

EFSをマウント。

sudo mount -t efs fs-xxxxxxxxxxxxxxxxx:/ /mnt/efs

マウントできたか確認。

df -h　127.0.0.1:/     8.0E     0  8.0E   0% /mnt/efs
表示されればOK

## サーバー側でテストファイルを作る

EFSサーバー01で、EFS上にテストファイルを作成。

echo "hello from bastion" | sudo tee /mnt/efs/test.txt

確認<br>
cat /mnt/efs/test.txt


## サーバー1からサーバー2へSSHする
ssh -i my-EC2-Key.pem ec2-user@EFSサーバー02のパブリックID

サーバー２にEFS用ツールをインストールから同様の手順でマウント

サーバー２で共有されたファイルを確認<br>
cat /mnt/efs/test.txt

以下のように表示されれば成功。<br>
hello from bastion

サーバー２でも同様にファイル作成

サーバー２からログアウト。<br>
exit

同じようにサーバー1から　cat　で確認
