# 1.CloudTrailログ保存用S3バケットを作成
S3バケットとは、AWSのストレージサービス Amazon S3 でファイルを保存するための「入れ物」

イメージとしては、クラウド上のフォルダや保管庫

S3<br>
↓<br>
バケット<br>
↓<br>
バケットを作成

```
バケット名前空間：アカウントのリージョナル名前空間（推奨）選択
パケット名：security-lab-cloudtrail-logs
```


Block Public Accessは有効
```
パブリックアクセスをすべてブロック：オン
```


バケットバージョニングとは、S3バケット内のオブジェクトを上書き・削除したときに、過去の版を残しておく機能
CloudTrailログの誤削除・誤上書き対策のため有効
```
バケットバージョニング：有効
```
デフォルト暗号化も有効にする
```
デフォルト暗号化：有効
暗号化タイプ：SSE-S3
```

オブジェクトロック
オブジェクトロックは「削除や上書きを防ぐ強い保護機能」なので、

必要がない場面で有効化すると、あとで削除・整理・コスト削減が面倒
```
オブジェクトロック：無効
```

## アカウント全体のBlock Public Accessの設定

S3<br>
↓<br>
アカウントと組織の設定
↓<br>
編集<br>
↓<br>
すべてオン<br>
↓<br>
変更を保存

```
パブリックアクセスをすべてブロック：オン
```

# 2.CloudTrail証跡を作成する
## CloudTrail画面へ進む
CloudTrail
↓
証跡
↓
証跡を作成


証跡名を入力
```
証跡名：security-lab-trail
```
ストレージの場所
先ほど作ったS3バケットを使う

既存のS3バケットを使用
```
security-lab-cloudtrail-logs
```

AWS KMS エイリアスを入力
```
alias/security-lab-cloudtrail-key
```
次へ

## ログイベントを選択する

管理イベントを有効に

```
管理イベント：有効
読み取り：有効
書き込み：有効
```
データイベントなどはログ量が多くなるから今回は無効に
```
データイベント：無効
Insightsイベント：無効
```


## CloudWatch Logs連携

今回はまずS3保存を優先するので、無効
```
CloudWatch Logs：無効
```

証跡を作成する


## CloudTrailの動作確認
## イベント履歴を見る
CloudTrail<br>
↓<br>
イベント履歴

ここに直近のAWS操作が表示される
```
CreateTrail
CreateBucket
RunInstances
AttachRolePolicy
AuthorizeSecurityGroupIngress
```
見るポイント
```
Event name
Event time
Username
AWS Region
Source IP address
Resources
```
## テスト操作をする

確認用に軽い操作をする

今回はEC2のタグを追加する

EC2<br>
↓<br>
security-lab-bastion<br>
↓<br>
タグ<br>
↓<br>
タグを管理<br>
↓<br>
Key: Lab<br>
↓<br>
Value: SecurityBasic<br>
↓<br>
保存

その後、CloudTrailのイベント履歴で確認<br>
検索欄で以下のようなイベントを探す
```
CreateTags
```

## S3にログが保存されているか確認
S3<br>
↓<br>
security-lab-cloudtrail-logs-xxxx<br>
↓<br>
AWSLogs

階層は以下の通り

AWSLogs/<br>
└ アカウントID/<br>
   └ CloudTrail/<br>
      └ ap-northeast-1/<br>
         └ YYYY/<br>
            └ MM/<br>
               └ DD/<br>

CloudTrailログはすぐに出ないことがあるので数分待つ
