# 1.Security Hubの有効化
Security Hubは、AWS環境のセキュリティ状態を集約して確認するサービス<br>
今回は主に以下を目的に使う
```
AWS Foundational Security Best Practicesのチェック
GuardDuty Findingの集約
AWS Config由来のコントロール評価
セキュリティ状態のダッシュボード化
```
GurdDutyやAWSConfigを集約して表示


## Security Hubを開く
Security Hub<br>
↓<br>
始める

始めるリージョンは現在の作業リージョンと一緒か確認

## ダッシュボードの確認

### 画面上部のフィルター
```
Workflow status = NEW	新規の検出結果
Workflow status = NOTIFIED	通知済みの検出結果
Record state = ACTIVE	まだ有効な検出結果
```
現在有効で、未対応または通知済みの検出結果を中心に表示

### セキュリティスコア
Security Hubで有効化しているセキュリティ基準のチェック項目に対して、どれくらい合格しているか

### セキュリティ基準
中央左の「セキュリティ基準」には、どの基準でチェックしているかが表示
```
CIS AWS Foundations Benchmark	AWSアカウント全体の基本的なセキュリティ基準
AWS Foundational Security Best Practices	AWS公式寄りの基本セキュリティチェック
```

### 調査結果が最も多い資産
右側の「調査結果が最も多い資産」は、問題が多く検出されているリソース(EC2など)一覧


### リージョン別の検出結果
リージョン別の脅威検出結果
