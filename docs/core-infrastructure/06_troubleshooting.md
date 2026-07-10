# Troubleshooting

## 概要

このドキュメントでは、AWS基礎インフラ環境の構築中に発生した問題と、その原因・対応方法をまとめる。



---

## 1. 踏み台サーバーにEC2 Instance Connectで接続できない

### 発生した問題

踏み台サーバーとして作成したEC2インスタンスに対して、EC2 Instance Connectを使用して接続しようとしたが、接続できなかった。

### 原因

EC2 Instance Connectは、AWS側の接続元IPからSSHしに来るため、マイIP だけだと失敗する場合がある。


### 対応方法

一時的にSSHの接続元を以下のように変更した。

```text
Type: SSH
Protocol: TCP
Port: 22
Source: 0.0.0.0/0
```

その後、EC2 Instance ConnectまたはSSH接続が成功することを確認した。

接続確認後は、セキュリティリスクを下げるため、すぐに接続元をマイIPに戻した



## 2. SSHでEC2に接続できない
### 発生した問題

SSHコマンドを使用してEC2へ接続しようとしたが、秘密鍵ファイルの権限に関するエラーが発生し、接続できなかった。

### 原因
秘密鍵ファイルの権限が広すぎたため、SSHクライアントが安全ではない鍵として判断し、接続を拒否した。

SSH秘密鍵は、他のユーザーから読み取れないように制限されている必要がある。

### 対応方法

秘密鍵ファイルの権限を制限した。

keyのある場所へ移動
```
cd C:\Users\test\Downloads
```
PC_Userに読み取り権限を付ける
```
icacls .\my-EC2-Key.pem /grant:r "DESKTOP-xxxxx\PC_User:R"	
```
グループの権限を削除する
```
icacls .\my-EC2-Key.pem /remove "Users"	Users
```

認証済みユーザー全体の権限を削除する
```
icacls .\my-EC2-Key.pem /remove "Authenticated Users"	
```
Everyone権限を削除する
 ```
icacls .\my-EC2-Key.pem /remove "Everyone"
```
現在の権限を確認する
```
icacls .\my-EC2-Key.pem	
```
SSH接続する際にはPowershellでもTeratermでも管理者権限であることに注意



## 3. Security Groupのインバウンドルールを編集できない
### 発生した問題

Security Groupのインバウンドルールを編集しようとしたが、既存ルールを期待通りに変更できなかった。

特に、既存のIPv4 CIDR指定のルールを、Security Group参照のルールに変更しようとした際に、うまく編集できなかった。

### 原因

Security Groupのルールでは、既存ルールの種類によっては、そのまま別形式のルールへ変更できない場合がある。

### 対応方法

既存のルールを直接編集するのではなく、以下の手順で対応した。
```text
1. 新しいインバウンドルールを追加する
2. 通信できることを確認する
3. 不要になった既存ルールを削除する
```
