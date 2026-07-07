# 1. EFSとNFSファイルシステム

EFSは、AWSが提供する共有ファイルストレージである。<br>
複数のEC2インスタンスから同じEFSをマウントすることで、ファイルを共有できる。<br>
EFSはNFSというファイル共有プロトコルを使用する。<br>
EFSを利用する場合、Subnet内にEFS Mount Targetを作成し、EC2からそのMount Targetへ接続する。<br>
EFS本体がSubnet内に存在するというより、EC2がEFSへ接続する入口であるMount TargetがSubnet内に作成される。<br>
これにより、複数のEC2間で同じファイルを共有できる。

# 2. RDSについて

RDSは、AWSが提供するマネージドデータベースサービスである。

今回の構築では、MySQLのRDSインスタンスを作成し、EC2から接続する構成を学んだ。

RDSを利用することで、OSやDBソフトウェアの一部管理をAWSに任せることができる。

RDS構築時には、以下の要素が重要である。

- DB Subnet Group
- Security Group
- Master username
- Master password
- Public access設定

特に、RDSをインターネットへ直接公開しない場合は、Public accessを無効化し、Private Subnet側に配置する。

接続元はPrivate EC2などに限定し、Security GroupでMySQLの3306番ポートを許可する。

Private EC2
↓ MySQL 3306
RDS MySQL
# 3. Elastic IPについて

Elastic IPは、AWSで利用できる固定のパブリックIPv4アドレスである。

通常、EC2のパブリックIPは停止・起動などで変わる場合があるが、Elastic IPを使うことで固定IPとして扱うことができる。

今回の構築では、NAT Gatewayを作成する際にElastic IPを利用した。

NAT GatewayにはElastic IPを関連付けることで、Private Subnet内のEC2がインターネットへ外向き通信できるようになる。
# 4. NAT Gatewayについて

NAT Gatewayは、Private Subnet内のEC2がインターネットへ外向き通信するために利用するサービスである。

Private EC2はパブリックIPを持たないため、そのままではインターネット上のパッケージリポジトリへアクセスできない。

今回の構築では、RDS接続確認に必要なMySQLクライアントなどをPrivate EC2にインストールするため、NAT Gatewayを利用した。

通信経路は以下の通り。

Private EC2
↓
Private Route Table
↓
NAT Gateway
↓
Internet Gateway
↓
Internet

NAT GatewayはPublic Subnetに配置し、Private SubnetのRoute Tableでは以下のように設定する。

0.0.0.0/0 → NAT Gateway

NAT Gatewayを利用することで、Private EC2はインターネットへ出ていくことはできるが、インターネット側からPrivate EC2へ直接接続することはできない。

Private EC2 → Internet: 可能
Internet → Private EC2: 不可
# 5. AMIについて

AMIは、EC2インスタンスを作成するためのテンプレートである。

AMIには、OS、初期設定、インストール済みソフトウェアなどが含まれる。

EC2を起動する際には、Amazon Linux、Ubuntu、Windows ServerなどのAMIを選択する。

今回の構築では、EC2インスタンス作成時にAMIを選択し、同じ種類のOSで複数のサーバーを作成できることを学んだ。

AMIを利用することで、同じ構成のサーバーを再作成しやすくなる。

# 6. スナップショットについて

スナップショットは、EBSボリュームなどの状態を保存するバックアップである。

EC2インスタンスで使用しているEBSボリュームのスナップショットを取得することで、障害時や作業前のバックアップとして利用できる。

スナップショットから新しいEBSボリュームを作成したり、AMI作成の元にしたりできる。

AMIとスナップショットは似ているが、役割が異なる。

項目	役割
AMI	EC2を作成するためのテンプレート
スナップショット	EBSボリュームなどのバックアップ

AMIの内部では、EBSスナップショットが利用されることがある。

# 7. ELB機能とALBについて

ELBは、Elastic Load Balancingの略で、AWSのロードバランシングサービス全体を指す。

ELBには複数の種類がある。

- ALB: Application Load Balancer
- NLB: Network Load Balancer
- CLB: Classic Load Balancer

今回の構築では、HTTP通信をWeb EC2へ振り分けるためにALBを利用した。

ALBは、HTTP/HTTPSなどのアプリケーション層の通信に適したロードバランサーである。

構成イメージは以下の通り。

Internet
↓
ALB
↓
Target Group
↓
Web EC2

ALBでは、Target GroupにEC2を登録し、Health Checkによって正常なターゲットかどうかを確認する。

Target GroupがHealthyになることで、ALBからEC2へ正常に通信が転送される。
