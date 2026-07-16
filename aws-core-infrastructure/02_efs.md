# 1.EFS(ファイル共有)とは
EFSはAWSが提供するクラウドネイティブなNFS互換のフルマネージドサービス。

EFSは 複数のEC2から同時に使える共有フォルダ のようなもの。

例えば、2台のLinuxサーバーがある場合、それぞれから同じEFSをマウントすると、同じディレクトリを見られる。



# 2. EFS用セキュリティグループを作成する

EFS専用のセキュリティグループを作成する。

```text
EC2
↓
セキュリティグループ
↓
セキュリティグループを作成
```

設定例は以下である。

```text
セキュリティグループ名: efs-sg
説明: Allow NFS from Linux EC2
VPC: <vpc-name>
```

インバウンドルールを追加する。

```text
タイプ: NFS
プロトコル: TCP
ポート: 2049
ソース: <bastion-sg>
```

さらに、内部Linux用のルールも追加する。

```text
タイプ: NFS
プロトコル: TCP
ポート: 2049
ソース: <private-linux-sg>
```

---

# 3. EFSファイルシステムを作成する

EFSファイルシステムを作成する。

```text
AWSコンソール
↓
EFS
↓
ファイルシステム
↓
ファイルシステムを作成
```

設定例は以下である。

```text
名前: <efs-name>
VPC: <vpc-name>
```

作成後、EFSのファイルシステムIDを確認する。

```text
例: fs-xxxxxxxxxxxxxxxxx
```

このファイルシステムIDは、後続のマウントコマンドで使用する。

---

# 4. マウントターゲットを確認する

EFSをEC2から利用するには、VPC内にマウントターゲットが必要である。

マウントターゲットは、EC2がEFSへ接続するための入口である。

確認場所は以下である。

```text
EFS
↓
ファイルシステム
↓
<efs-name> を選択
↓
ネットワーク
```

マウントターゲットに設定するセキュリティグループは、作成したEFS用セキュリティグループを指定する。

```text
セキュリティグループ: efs-sg
```

---

# 5. 踏み台サーバーにEFSをマウントする

踏み台サーバーへログインする。

```text
EC2
↓
インスタンス
↓
踏み台サーバーを選択
↓
接続
↓
EC2 Instance Connect
↓
接続
```

ログイン後、EFS用ツールをインストールする。

```bash
sudo dnf install -y amazon-efs-utils
```

マウント先ディレクトリを作成する。

```bash
sudo mkdir -p /mnt/efs
```

EFSをマウントする。

```bash
sudo mount -t efs <efs-file-system-id>:/ /mnt/efs
```

マウントできたか確認する。

```bash
df -h
```

---

# 6. 踏み台サーバー側でテストファイルを作成する

踏み台サーバーで、EFS上にテストファイルを作成する。

```bash
echo "hello from bastion" | sudo tee /mnt/efs/test.txt
```

作成したファイルを確認する。

```bash
cat /mnt/efs/test.txt
```

以下のように表示されれば成功である。

```text
hello from bastion
```

---

# 7. 踏み台サーバーから内部LinuxへSSH接続する

踏み台サーバーから内部LinuxへSSH接続する。

```bash
ssh -i <key-file>.pem ec2-user@<private-linux-private-ip>
```

ログイン後、プロンプトが内部Linux側に切り替わる。

```text
[ec2-user@ip-xx-xx-xx-xx ~]$
```

---

# 8. 内部LinuxにEFSをマウントする

内部Linux側でも、EFS用ツールをインストールする。

```bash
sudo dnf install -y amazon-efs-utils
```

マウント先ディレクトリを作成する。

```bash
sudo mkdir -p /mnt/efs
```

EFSをマウントする。

```bash
sudo mount -t efs <efs-file-system-id>:/ /mnt/efs
```

マウントできたか確認する。

```bash
df -h
```

---

# 9. 内部Linux側で共有ファイルを確認する

内部Linux側で、踏み台サーバー側で作成したファイルを確認する。

```bash
cat /mnt/efs/test.txt
```

以下のように表示されれば成功である。

```text
hello from bastion
```

---

# 10. 内部Linux側からファイルを作成する

内部Linux側から、EFS上にファイルを作成する。

```bash
echo "hello from private linux" | sudo tee /mnt/efs/private.txt
```

---

# 11. 踏み台サーバー側で共有ファイルを確認する

内部Linuxからログアウトする。

```bash
exit
```

踏み台サーバー側で、内部Linuxが作成したファイルを確認する。

```bash
cat /mnt/efs/private.txt
```

以下のように表示されれば成功である。

```text
hello from private linux
```

これにより、踏み台サーバーと内部Linuxの2台で同じEFSを共有できた状態である。
