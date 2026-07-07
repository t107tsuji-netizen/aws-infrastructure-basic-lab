# 10.CloudTrailログ保存用S3バケットを作成
S3バケットとは、AWSのストレージサービス Amazon S3 でファイルを保存するための「入れ物」です。イメージとしては、クラウド上のフォルダや保管庫に近い

S3
↓
バケット
↓
バケットを作成


バケット名前空間：アカウントのリージョナル名前空間（推奨）選択
パケット名：security-lab-cloudtrail-logs


マルチリージョンはデフォルトでオン

Block Public Access
これは必ず有効に。
パブリックアクセスをすべてブロック：オン

バケットバージョニング
バケットバージョニングとは、S3バケット内のオブジェクトを上書き・削除したときに、過去の版を残しておく機能

有効でOKです。
バケットバージョニング：有効
CloudTrailログの誤削除・誤上書き対策


暗号化
デフォルト暗号化も有効でOKです。
デフォルト暗号化：有効
暗号化タイプ：SSE-S3


オブジェクトロックは？
今回の初版では、無効でOKです。
オブジェクトロック：無効

アカウント全体のBlock Public Accessの設定

S3
↓
左メニュー
アカウントと組織の設定
↓
編集
↓
すべてオン
↓
変更を保存

設定はこれです。

パブリックアクセスをすべてブロック：オン



# 10.CloudTrail証跡を作成する
9-1. CloudTrail画面へ進む
CloudTrail
↓
証跡
↓
証跡を作成


証跡名を入力
証跡名：security-lab-trail

ストレージの場所
先ほど作ったS3バケットを使います。

既存のS3バケットを使用
security-lab-cloudtrail-logs


AWS KMS エイリアスを入力
alias/security-lab-cloudtrail-key

次へ

ログイベントを選択する

まずは管理イベントを有効にします。

管理イベント：有効
読み取り：有効
書き込み：有効

Basic Labでは、まずこれで十分です。

データイベント：無効でOK
Insightsイベント：無効でOK



CloudWatch Logs連携

今回はまずS3保存を優先するので、無効でOKです。

CloudWatch Logs：無効

あとで発展版として、CloudTrailログをCloudWatch Logsへ送って、メトリクスフィルターやアラームにつなげることもできます


証跡を作成する

最後に確認します。

証跡を作成



## CloudTrailの動作確認
10-1. イベント履歴を見る
CloudTrail
↓
イベント履歴

ここに直近のAWS操作が表示されます。

例えば以下のようなイベントが見えます。

CreateTrail
CreateBucket
RunInstances
AttachRolePolicy
AuthorizeSecurityGroupIngress

見るポイント：

Event name
Event time
Username
AWS Region
Source IP address
Resources
10-2. テスト操作をする

確認用に軽い操作をします。

おすすめはEC2のタグ追加です。

EC2
↓
security-lab-bastion
↓
タグ
↓
タグを管理
↓
Key: Lab
↓
Value: SecurityBasic
↓
保存

その後、CloudTrailのイベント履歴で確認します。

検索欄で以下のようなイベントを探します。

CreateTags

これで、AWS操作がCloudTrailに記録されていることを示せます。

10-3. S3にログが保存されているか確認
S3
↓
security-lab-cloudtrail-logs-xxxx
↓
AWSLogs

階層はだいたいこうなります。

AWSLogs/
└ アカウントID/
   └ CloudTrail/
      └ ap-northeast-1/
         └ YYYY/
            └ MM/
               └ DD/

CloudTrailログはすぐに出ないことがあります。数分待ってから確認
