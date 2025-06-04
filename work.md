ではセクションごとに内容を作成していきます。
下記の要件をもとにして出力してください。

前提:"
・AzureDevOps上で動作するTerraformがアラートルールをデプロイするシステム
・## 8. 運用設計は別に自動プロビジョニングシステムとして設計書が存在するため基本的にそれを参照するように記述すること
"

対象:"
## 8. 運用設計
### 8.1 デプロイメント手順
#### 8.1.1 Webサービス経由のデプロイ
- 実行フロー
- エラーハンドリング

#### 8.1.2 Excel/PowerShell経由のデプロイ
- 実行手順
- 検証方法

### 8.2 監視・アラート
- システム監視項目
- 障害時の通知設定

### 8.3 バックアップ・リカバリ
- 構成情報のバックアップ
- 障害復旧手順
"

出力要件:"
・ITの分野で作成されるシステムの基本設計書
・である。口調で統一
・日本語化として正しいかのチェック
・専門的にわかりやすく
"

---
# Azureリスキーアラート作成システム 基本設計書

## 1. はじめに
### 1.1 目的
### 1.2 用語一覧

## 2. システム概要
### 2.1 システムの目的と役割
#### 2.1.1 システムの目的
本システムは、Azure環境におけるセキュリティリスクを早期に検知し、適切な対応を可能にするため、リスキーアラートルールの作成・管理を自動化することを目的とする。従来の手動によるアラート設定では、設定ミスや作業遅延が発生する可能性があったが、本システムにより、標準化されたアラートルールを迅速かつ正確にデプロイすることが可能となる。

#### 2.1.2 システムの役割
本システムは以下の役割を担う：

アラートルールの自動デプロイ  
Terraformを使用したInfrastructure as Codeによるアラートルールの定義と自動デプロイを実現する  
Azure DevOpsのパイプラインを通じて、コード化されたアラートルールを安全にAzure環境へ適用する  

標準化されたアラート管理  
組織全体で統一されたリスキーアラートルールを維持管理する  
バージョン管理により、アラートルールの変更履歴を追跡可能にする  

複数のインターフェース提供  
自社Webサービスからの自動連携により、システム間の統合を実現する  
Excelヒアリングシートを活用した非技術者向けのインターフェースを提供する  

運用効率の向上  
アラートルールの作成・変更・削除作業を自動化し、運用負荷を軽減する  
設定ミスを防止し、アラートの品質を向上させる  


#### 2.1.3 期待される効果
セキュリティインシデントの早期発見と対応時間の短縮  
アラート設定作業の工数削減  
設定ミスによる誤検知・見逃しの防止  
アラートルールの一元管理による運用品質の向上  


### 2.2 システム構成概要
#### 2.2.1 システム全体構成
本システムは、Azure DevOpsを中心としたCI/CDパイプラインと、Terraformによるインフラストラクチャ管理を組み合わせた構成である。アラートルールの定義からデプロイまでの一連のプロセスを自動化し、安全かつ効率的な運用を実現する。


#### 2.2.2 全体構成図
- システム全体の構成図（作成予定）


#### 2.2.3 主要コンポーネント

Azure DevOps環境  
Gitリポジトリ: Terraformコードおよび設定ファイルのバージョン管理を行う  
Pipelinesサービス: ビルド・リリースパイプラインによる自動デプロイメントを実行する  
Artifacts: Terraformモジュールや共通設定ファイルを格納する  

Terraform実行環境  
Terraform本体: バージョン1.5以上を使用し、HCL（HashiCorp Configuration Language）でアラートルールを定義する  
Azure Provider: Azure Resource Managerと連携し、リソースの作成・更新・削除を実行する  
State管理: Azure Storage Accountに保存されるTerraform Stateファイルにより、インフラストラクチャの現在状態を管理する  

トリガーインターフェース  
Webサービス連携: REST APIを通じて外部システムからパイプライン実行を起動する
PowerShellスクリプト: Excelヒアリングシートから抽出したパラメータを基に、ローカル環境からパイプラインを実行する


Azure環境

Azure Monitor: 作成されたアラートルールが配置され、実際の監視を実行する
Log Analytics Workspace: アラート条件の評価に使用するログデータを格納する
Action Groups: アラート発生時の通知先や対応アクションを定義する


#### 2.2.4 処理の流れ
トリガーインターフェースが実行要求を受信する
Azure DevOps Pipelineが起動し、パラメータの検証を実施する
Terraformコードが実行され、定義されたアラートルールをAzure環境にデプロイする
デプロイ結果がログに記録され、要求元へ応答を返却する

#### 2.2.5 システム間連携
認証連携: Azure Active Directoryによるシングルサインオンを実現する
監視連携: デプロイされたアラートは既存の監視システムと統合される
通知連携: アラート発生時は既存のインシデント管理システムへ自動連携される

## 3. 前提条件と制約事項
### 3.1 前提条件

#### 3.1.1 インフラストラクチャ要件  
Azure商用環境のサブスクリプションが利用可能であること  
Azure DevOps組織が作成済みであり、必要なプロジェクトが設定されていること  
Terraform実行用のセルフホステッドエージェントまたはMicrosoft-hostedエージェントが利用可能であること  
Azure Storage AccountがTerraform Stateファイル保存用に準備されていること  

#### 3.1.2 ライセンス要件
Azure DevOpsの必要なライセンス（Basic以上）が割り当てられていること  
Azure Monitorの必要な機能が利用可能なサブスクリプションレベルであること  
PowerShell実行環境においてExcelファイルを操作するためのOfficeライセンスまたは互換ライブラリが利用可能であること  

#### 3.1.3 アクセス権限要件
Terraformサービスプリンシパルに以下の権限が付与されていること：  

Azure Monitor Contributor  
Log Analytics Contributor  
対象リソースグループへのContributor権限  

Azure DevOpsパイプライン実行ユーザーに必要なプロジェクト権限が付与されていること  
Webサービス連携用のAPIキーまたはサービスアカウントが発行されていること  

### 3.1.4 技術要件
Terraform バージョン1.5.0以上がインストールされていること  
Azure Provider for Terraform バージョン3.0以上が利用可能であること  
PowerShell 7.0以上がインストールされていること  
Git クライアントがインストールされていること  

### 3.2 制約事項
#### 3.2.1 技術的制約
アラートルールの作成は、Azure Monitorがサポートする条件とアクションに限定される  
Terraform Stateファイルの同時実行制御により、パイプラインの並列実行は制限される  
1回のデプロイで作成可能なアラートルール数は、Azure APIのレート制限に従う（1分間あたり最大1200回の書き込み操作） 

#### 3.2.2 運用上の制約
Terraformコードの変更は、承認されたプルリクエストを通じてのみ反映される  
本番環境へのデプロイは、定められた変更管理プロセスに従う必要がある  
アラートルールの削除は、事前の影響調査と承認を必要とする  


#### 3.2.3 セキュリティ上の制約
すべての通信はHTTPS/TLSで暗号化される必要がある  
機密情報（APIキー、接続文字列など）はAzure Key Vaultに格納し、直接コードに記述してはならない  
アクセスログは90日以上保存し、監査要件を満たす必要がある  


#### 3.2.4 パフォーマンス上の制約
大規模なアラートルール変更（100件以上）は、段階的にデプロイする必要がある  
パイプライン実行時間は最大60分に制限される  

### 3.3 依存関係
#### 3.3.1 外部システムとの依存関係

自社Webサービス  
APIエンドポイントの可用性に依存する  
API仕様の変更は本システムへの影響分析が必要である  

Active Directory  
ユーザー認証およびグループ管理に依存する  
ADのメンテナンス時は認証機能が制限される可能性がある  


既存監視システム  
アラート通知の転送先として依存する  
監視システムの仕様変更時は、Action Groupsの設定変更が必要となる  


#### 3.3.2 Azureサービスの依存関係  
Azure Monitor: アラートルールの実行基盤として依存する  
Log Analytics Workspace: ログデータの収集と分析に依存する  
Azure Key Vault: 機密情報の安全な保管に依存する  


## 4. 機能要件
### 4.1 主要機能一覧

本システムは以下の主要機能を提供する：  

| 項番 | 機能名称 | 概要 |
|------|----------|------|
| 1 | アラート作成機能 | Terraformを使用してAzure Monitorのアラートルールを新規作成する |
| 2 | アラート更新機能 | 既存のアラートルールのパラメータや条件を変更する |
| 3 | アラート削除機能 | 不要になったアラートルールを安全に削除する |
| 4 | パラメータ検証機能 | 入力されたパラメータの妥当性を検証する |
| 5 | 実行ログ記録機能 | すべての操作履歴をログとして記録する |

### 4.2 各機能説明
#### 4.2.1 アラート作成・削除機能
##### 4.2.1.1 Terraformによるアラート定義
**アラートルールの定義方法**  
Terraformのリソース定義として、以下の形式でアラートルールを記述する：  

```hcl
resource "azurerm_monitor_metric_alert" "risky_signin_alert" {
  name                = var.alert_name
  resource_group_name = var.resource_group_name
  scopes              = [var.target_resource_id]
  description         = var.alert_description
  severity            = var.alert_severity

  criteria {
    metric_namespace = "Microsoft.AAD/SigninLogs"
    metric_name      = "RiskySignins"
    aggregation      = var.aggregation_method
    operator         = var.comparison_operator
    threshold        = var.threshold_value
  }

  action {
    action_group_id = var.action_group_id
  }
}
```

**パラメータ管理**  
本システムでは、パラメータを以下の2つの方法で管理する：  
1. **基本パラメータ（config.json）**  
   - Azure DevOpsリポジトリに配置される設定ファイル  
   - リージョン、共通タグ、環境設定などの静的な値を定義  
   - 例：  
   ```json
   {
     "common": {
       "location": "japaneast",
       "tags": {
         "Environment": "Production",
         "ManagedBy": "Terraform"
       },
       "default_action_group": "/subscriptions/xxx/resourceGroups/xxx/providers/microsoft.insights/actionGroups/default"
     },
     "environments": {
       "prod": {
         "resource_group_name": "rg-monitoring-prod",
         "log_analytics_workspace_id": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OperationalInsights/workspaces/law-prod"
       }
     }
   }
   ```

2. **動的パラメータ（インプット）**  
   - パイプライン実行時に外部から受け取るパラメータ  
   - アラート名、閾値、重要度など、アラートルールごとに異なる値  
   - パイプライン変数として定義され、Terraform実行時に渡される  

##### 4.2.1.2 アラート種別
**リスキーサインインアラート**  
本システムでは、Azure Active Directoryのリスキーサインインを検知するアラートを管理する。リスキーサインインとは、以下のような不審なサインイン活動を指す：  
- **検知条件**  
  - 通常と異なる場所からのサインイン  
  - 匿名IPアドレスからのサインイン  
  - マルウェアに感染したデバイスからのサインイン  
  - 不審なIPアドレスからのサインイン  
  - 漏洩した資格情報によるサインイン  

- **アラート設定項目**  
  - リスクレベル（低、中、高）  
  - 評価期間（5分、15分、30分、1時間）  
  - 発生回数の閾値  
  - 通知の重要度（1〜4） 

- **監視対象**  
  - Azure Active Directoryのサインインログ  
  - 条件付きアクセスポリシーの評価結果  
  - Identity Protectionのリスク評価  

#### 4.2.2 トリガー機能
##### 4.2.2.1 Azure DevOpsパイプライントリガー
パイプラインは以下の方法で実行される：  

1. **手動実行**  
   - Azure DevOps Webインターフェースからの直接実行  
   - パラメータを画面から入力して実行  

2. **スケジュール実行**  
   - 定期的なアラートルールの更新や検証  
   - cronフォーマットでスケジュール定義  

3. **コミットトリガー**  
   - 特定ブランチへのコミット時に自動実行  
   - プルリクエストのマージ時に本番環境へ適用  

##### 4.2.2.2 パラメータ入力と検証
**入力パラメータ**  

パイプライン実行時に以下のパラメータを受け取る：  

- `alert_name`: アラート名（必須）
- `risk_level`: リスクレベル（low/medium/high）
- `threshold_count`: 閾値となる発生回数
- `evaluation_window`: 評価期間（分単位）
- `severity`: アラートの重要度（1-4）
- `enabled`: アラートの有効/無効

**パラメータ検証処理**

1. 必須パラメータの存在確認
2. データ型の妥当性検証
3. 値の範囲チェック（例：severity は1〜4の範囲内）
4. 既存アラートとの重複確認
5. 依存関係の整合性確認理

## 5. 非機能要件
### 5.1 可用性
#### 5.1.1 SLA目標  
本システムは以下のサービスレベル目標を設定する：  

- **システム稼働率**: 99.5%（月間）  
  - 計画停止時間を除く  
  - 月間ダウンタイム許容時間：約3.6時間  
  
- **Azure DevOps依存部分**  
  - Microsoft提供のSLA（99.9%）に準拠する  
  - 自社管理のセルフホステッドエージェント使用時は95%を目標とする  

- **アラートデプロイ成功率**: 99%以上
  - 正常なパラメータでの実行における成功率
  - ネットワーク障害やAzure側の問題による失敗は除外する

#### 5.1.2 冗長化方針
1. **パイプラインエージェントの冗長化**
   - セルフホステッドエージェントを複数台構成する（最小2台）
   - エージェントプールによる自動フェイルオーバーを実現する
   - 各エージェントは異なる可用性ゾーンに配置する

2. **Terraform State管理の冗長化**
   - Azure Storage AccountのGRS（地理冗長ストレージ）を使用する
   - State Lockingによる同時実行制御を実装する
   - 定期的なStateファイルのバックアップを実施する（日次）

3. **構成情報の冗長化**
   - Azure DevOps Gitリポジトリの自動バックアップ
   - 重要な設定ファイル（config.json）の世代管理
   - 災害復旧用の別リージョンへのレプリケーション

### 5.2 性能

#### 5.2.1 レスポンスタイム要件

| 処理種別 | 目標時間 | 最大許容時間 |
|----------|----------|--------------|
| パイプライン起動 | 30秒以内 | 60秒 |
| パラメータ検証 | 10秒以内 | 30秒 |
| 単一アラート作成 | 2分以内 | 5分 |
| 単一アラート更新 | 2分以内 | 5分 |
| 単一アラート削除 | 1分以内 | 3分 |
| 10件一括処理 | 10分以内 | 20分 |

#### 5.2.2 同時実行数

- **パイプライン同時実行数**: 最大5
  - Terraform State Lockingにより、同一環境への同時デプロイは制限される
  - 異なる環境（開発/検証/本番）への同時デプロイは可能とする

- **リソース制限**
  - 各パイプライン実行に割り当てるCPU: 2コア
  - 各パイプライン実行に割り当てるメモリ: 4GB
  - 最大実行時間: 60分（タイムアウト設定）

- **API制限への対応**
  - Azure Resource Manager APIレート制限（1時間あたり1200回）を考慮する
  - 必要に応じてリトライロジックとバックオフ処理を実装する

### 5.3 拡張性

#### 5.3.1 スケーリング方針

1. **水平スケーリング**
   - パイプラインエージェントの追加による処理能力向上
   - 最大10台までのエージェント追加を想定する
   - 負荷分散はAzure DevOpsのエージェントプール機能で実現する

2. **垂直スケーリング**
   - エージェントマシンのスペック向上（CPU/メモリ）
   - Standard_D4s_v3からStandard_D8s_v3への変更を想定する

3. **モジュール化による拡張性**
   - Terraformモジュールの分割による並列処理の実現
   - アラートタイプごとのモジュール化により、新規アラートタイプの追加を容易にする

#### 5.3.2 将来的な拡張計画

1. **対応アラートタイプの拡張**
   - 現在：リスキーサインインアラートのみ
   - 6ヶ月後：Azure Monitorの全メトリックアラートに対応
   - 1年後：ログベースアラート、カスタムメトリックアラートに対応

2. **マルチクラウド対応**
   - 将来的にAWS CloudWatchアラートへの対応を検討する
   - Terraformのマルチプロバイダー機能を活用する

3. **自動化機能の拡張**
   - AIによるアラート閾値の自動調整機能
   - 過去のインシデントデータに基づく最適化
   - アラート発生パターンの分析と予測

### 5.4 セキュリティ

#### 5.4.1 アクセス制御

1. **認証方式**
   - Azure Active Directory（Azure AD）による統合認証
   - 多要素認証（MFA）の必須化
   - サービスプリンシパルによるシステム間認証

2. **認可方式**
   - RBACによる最小権限の原則の適用
   - 以下の役割を定義する：
     - アラート管理者：全機能の実行権限
     - アラート作成者：作成・更新権限のみ
     - アラート閲覧者：読み取り権限のみ

3. **Azure DevOpsのアクセス制御**
   - プロジェクトレベルでのアクセス権限設定
   - ブランチポリシーによるmainブランチの保護
   - プルリクエストでの承認プロセスの必須化

#### 5.4.2 暗号化設定

1. **通信の暗号化**
   - すべての通信はTLS 1.2以上で暗号化する
   - Azure DevOpsとAzure間の通信はHTTPSを使用する
   - 証明書の有効期限監視と自動更新を実施する

2. **保存データの暗号化**
   - Terraform StateファイルはAzure Storage Service Encryption（256ビットAES）で暗号化する
   - 機密情報（APIキー、接続文字列）はAzure Key Vaultに保存する
   - Key Vaultへのアクセスは監査ログで記録する

3. **シークレット管理**
   - パイプライン内でのシークレットはAzure DevOpsのSecret変数として管理する
   - シークレットのローテーション（90日ごと）を実施する
   - シークレットの平文での記録を禁止する

## 6. システムアーキテクチャ

### 6.1 処理フロー図

#### 6.1.1 全体処理フロー
本システムの処理は、Azure DevOpsパイプラインを中心に以下の流れで実行される：

```
[開始] 
  ↓
[パイプライン起動]
  ├─ 手動実行
  ├─ スケジュール実行
  └─ コミットトリガー
  ↓
[パラメータ入力/読み込み]
  ├─ パイプライン変数
  └─ config.json
  ↓
[パラメータ検証]
  ├─ 必須項目チェック
  ├─ 値の妥当性検証
  └─ 依存関係確認
  ↓
[Terraform初期化]
  ├─ プロバイダー設定
  └─ State取得
  ↓
[Terraform Plan実行]
  ├─ 変更内容の確認
  └─ ドライラン結果
  ↓
[承認プロセス]（本番環境のみ）
  ↓
[Terraform Apply実行]
  ├─ リソース作成/更新/削除
  └─ State更新
  ↓
[実行結果記録]
  ├─ ログ出力
  └─ 通知送信
  ↓
[終了]
```

#### 6.1.2 エラー処理フロー
エラー発生時は以下の処理を実行する：

1. エラー内容のログ記録
2. Terraform Stateのロック解除（必要に応じて）
3. 管理者への通知送信
4. パイプラインの停止

### 6.2 データ設計

#### 6.2.1 入力データ仕様

##### 6.2.1.1 パイプライン実行パラメータ

**必須パラメータ**

| パラメータ名 | データ型 | 説明 | 例 |
|------------|----------|------|-----|
| environment | string | デプロイ対象環境 | "prod", "dev", "staging" |
| alert_name | string | アラート名（英数字とハイフン） | "risky-signin-alert-01" |
| action | string | 実行アクション | "create", "update", "delete" |
| risk_level | string | リスクレベル | "low", "medium", "high" |
| threshold_count | integer | 閾値（発生回数） | 5 |
| enabled | boolean | アラートの有効/無効 | true, false |

**オプションパラメータ**

| パラメータ名 | データ型 | 説明 | デフォルト値 |
|------------|----------|------|-------------|
| evaluation_window | string | 評価期間 | "PT5M" (5分) |
| severity | integer | 重要度（1-4） | 3 |
| description | string | アラートの説明 | 自動生成 |
| tags | object | タグ情報 | {} |

##### 6.2.1.2 設定ファイル（config.json）

リポジトリに配置されるconfig.jsonの構造：

```json
{
  "environments": {
    "prod": {
      "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "resource_group_name": "rg-monitoring-prod",
      "location": "japaneast",
      "log_analytics_workspace_id": "/subscriptions/.../workspaces/law-prod",
      "action_group_ids": {
        "high": "/subscriptions/.../actionGroups/ag-high-prod",
        "medium": "/subscriptions/.../actionGroups/ag-medium-prod",
        "low": "/subscriptions/.../actionGroups/ag-low-prod"
      }
    },
    "dev": {
      // 開発環境の設定
    }
  },
  "defaults": {
    "tags": {
      "System": "RiskyAlertSystem",
      "ManagedBy": "Terraform",
      "Version": "1.0"
    },
    "alert_settings": {
      "frequency": "PT5M",
      "window_size": "PT5M",
      "auto_mitigate": true
    }
  }
}
```

#### 6.2.2 Terraformパラメータ

**variables.tf の構成**

```hcl
variable "environment" {
  description = "デプロイ対象環境"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "環境は dev, staging, prod のいずれかである必要があります。"
  }
}

variable "alert_name" {
  description = "アラート名"
  type        = string
  validation {
    condition     = can(regex("^[a-z0-9-]+$", var.alert_name))
    error_message = "アラート名は英小文字、数字、ハイフンのみ使用可能です。"
  }
}

variable "risk_level" {
  description = "リスクレベル"
  type        = string
  validation {
    condition     = contains(["low", "medium", "high"], var.risk_level)
    error_message = "リスクレベルは low, medium, high のいずれかである必要があります。"
  }
}

variable "threshold_count" {
  description = "閾値（発生回数）"
  type        = number
  validation {
    condition     = var.threshold_count > 0 && var.threshold_count <= 100
    error_message = "閾値は1から100の間である必要があります。"
  }
}
```

**terraform.tfvars の管理方法**

- パイプライン実行時に動的に生成される
- パイプライン変数とconfig.jsonの値を組み合わせて作成
- 実行後は自動的に削除される（セキュリティ対策）
- Gitリポジトリにはコミットしない

#### 6.2.3 ログ・監査データ

**実行ログの保存**

1. **Azure DevOpsビルドログ**
   - 保存期間：90日間
   - 内容：パイプライン実行の全履歴
   - アクセス権限：プロジェクトメンバーのみ

2. **Terraformログ**
   - ログレベル：INFO以上（本番環境はDEBUG）
   - 出力先：Azure Storage Account（専用コンテナ）
   - 形式：JSON形式で構造化
   - 保存期間：1年間

3. **アプリケーションログ**
   - 出力先：Azure Log Analytics Workspace
   - 保存期間：90日間
   - クエリ例：
   ```kusto
   TerraformLogs_CL
   | where TimeGenerated > ago(7d)
   | where Environment_s == "prod"
   | where Action_s == "apply"
   | project TimeGenerated, User_s, AlertName_s, Result_s
   ```

**監査証跡の管理**

1. **記録対象**
   - 実行者情報（ユーザーID、IPアドレス）
   - 実行日時（UTC）
   - 実行内容（作成/更新/削除）
   - パラメータ値（機密情報はマスキング）
   - 実行結果（成功/失敗）

2. **監査ログ形式**
   ```json
   {
     "timestamp": "2024-01-15T10:30:00Z",
     "execution_id": "run-12345",
     "user": "user@example.com",
     "ip_address": "192.168.1.100",
     "action": "create",
     "resource_type": "alert_rule",
     "resource_name": "risky-signin-alert-01",
     "environment": "prod",
     "status": "success",
     "duration_seconds": 120
   }
   ```

3. **コンプライアンス要件**
   - 改ざん防止のため書き込み専用アカウントで記録
   - 定期的な監査レポートの自動生成（月次）
   - 長期保管用のアーカイブ（3年間）

### 6.3 インフラ設計

本システムのインフラ設計については、「自動プロビジョニングシステム基本設計書」を参照すること。特に以下の章節が本システムに関連する：

- 第3章「Azure DevOps環境構成」
  - 3.1 組織・プロジェクト構成
  - 3.2 エージェントプール設計
  - 3.3 アーティファクト管理

- 第4章「CI/CDパイプライン設計」
  - 4.1 パイプラインテンプレート
  - 4.2 変数グループ管理
  - 4.3 承認ゲート設定

- 第5章「Terraform実行環境」
  - 5.1 Terraformバージョン管理
  - 5.2 プロバイダー設定
  - 5.3 State管理設計

本システム固有の設定として、以下の点に留意すること：

1. **パイプライン名**: "terraform-risky-alert-deployment"
2. **変数グループ名**: "risky-alert-params"
3. **State保存先**: "terraform-state-risky-alerts"コンテナ
4. **承認者グループ**: "RiskyAlertApprovers"（本番環境のみ）

## 7. セキュリティ設計

本システムのセキュリティ設計については、「自動プロビジョニングシステムセキュリティ設計書」を基本とする。本章では、リスキーアラート作成システム固有のセキュリティ要件について記述する。

### 7.1 認証・認可

#### 7.1.1 Azure ADとの連携

本システムの認証は、Azure Active Directory（Azure AD）を中心に構成される。詳細な認証フローについては、「自動プロビジョニングシステムセキュリティ設計書」の第2章「認証アーキテクチャ」を参照すること。

**本システム固有の設定**

1. **ユーザー認証**
   - Azure DevOpsへのアクセスはAzure ADのシングルサインオン（SSO）を使用する
   - 条件付きアクセスポリシーにより、社内ネットワークまたはVPN接続を必須とする
   - 管理者権限を持つユーザーには多要素認証（MFA）を強制する

2. **サービスプリンシパル認証**
   - Azure DevOpsプロジェクト専用のサービスプリンシパルを使用する
   - サービスプリンシパル名：`sp-devops-risky-alert-{environment}`
   - 証明書ベースの認証を採用し、パスワード認証は使用しない

#### 7.1.2 ロールベースアクセス制御

**Azure DevOpsのロール設定**

| ロール名 | 権限 | 対象者 |
|---------|------|--------|
| RiskyAlert-Admin | パイプライン編集、実行、承認 | システム管理者 |
| RiskyAlert-Developer | パイプライン実行、コード変更 | 開発チーム |
| RiskyAlert-Operator | パイプライン実行のみ | 運用チーム |
| RiskyAlert-Viewer | 読み取りのみ | 監査担当者 |

**Azureリソースへの権限設定**

サービスプリンシパルには以下の最小権限を付与する：

```
スコープ: /subscriptions/{subscription-id}/resourceGroups/{rg-monitoring}
ロール: 
  - Monitoring Contributor (アラートルールの作成・更新・削除)
  - Log Analytics Reader (ログクエリの実行)
  - Reader (リソース情報の参照)
```

### 7.2 機密情報管理

#### 7.2.1 Azure DevOpsによるシークレット管理

本システムでは、Azure Key Vaultを使用せず、Azure DevOpsの機能を活用して機密情報を管理する。

**シークレット変数の管理**

1. **パイプライン変数**
   - サービスプリンシパルの証明書パスワード
   - Terraform Backendのアクセスキー
   - 通知用のWebhook URL
   - すべてのシークレット変数は「Secret」タイプとして設定する

2. **変数グループ**
   - 環境ごとに変数グループを作成：`vg-risky-alert-{environment}`
   - 変数グループへのアクセスは必要最小限のパイプラインに限定する
   - 変数グループの変更は承認プロセスを必須とする

**シークレットの取り扱い規則**

- シークレット値はログに出力されないよう、Azure DevOpsが自動的にマスキングする
- パイプライン実行時のみメモリ上で復号化され、永続化されない
- シークレットを含む成果物（artifacts）の作成を禁止する

#### 7.2.2 サービス接続の管理

**Azure Resource Manager接続**

- 接続名：`sc-azure-risky-alert-{environment}`
- 認証方式：サービスプリンシパル（証明書）
- スコープ：サブスクリプションレベル
- 有効期限：1年（年次更新）

**接続の保護設定**

- 承認されたパイプラインのみが使用可能
- 接続情報の編集は管理者ロールのみ
- 使用履歴はAzure DevOpsの監査ログに記録される

### 7.3 監査ログ

#### 7.3.1 アクセスログ

**記録対象のアクセスログ**

1. **Azure DevOpsアクセスログ**
   - ユーザーのサインイン/サインアウト
   - パイプラインの実行開始/終了
   - リポジトリへのアクセス（clone, pull, push）
   - 設定変更（パイプライン、変数、サービス接続）

2. **Azureリソースアクセスログ**
   - サービスプリンシパルによるAPI呼び出し
   - アラートルールの参照
   - Log Analyticsへのクエリ実行

**ログの保存と分析**

- Azure DevOpsの監査ログ：組織レベルで90日間保存
- Azureアクティビティログ：Log Analytics Workspaceに転送し、1年間保存
- 異常なアクセスパターンの検知：
  ```kusto
  AzureActivity
  | where TimeGenerated > ago(24h)
  | where Caller contains "sp-devops-risky-alert"
  | where ActivityStatus != "Succeeded"
  | project TimeGenerated, Caller, OperationName, ActivityStatus
  ```

#### 7.3.2 変更履歴

**構成変更の追跡**

1. **コード変更履歴**
   - Gitによるバージョン管理
   - すべての変更はプルリクエスト経由
   - コミットメッセージの規約：`[環境][変更種別] 変更内容`
   - 例：`[PROD][UPDATE] リスキーサインインアラートの閾値を5から3に変更`

2. **インフラ変更履歴**
   - Terraform Stateの変更履歴をAzure Storage Accountのバージョニング機能で保持
   - 各実行の plan 出力をパイプライン成果物として30日間保存
   - 重要な変更は変更管理票と紐付け

**コンプライアンス監査対応**

- 月次で以下のレポートを自動生成する：
  - アラートルール変更一覧
  - 実行者別のパイプライン実行統計
  - 失敗した実行の原因分析
  - 未承認アクセスの試行（存在する場合）

詳細なセキュリティ監査手順については、「自動プロビジョニングシステムセキュリティ設計書」の第5章「セキュリティ監査」を参照すること。

## 8. 運用設計

本システムの運用設計については、「自動プロビジョニングシステム運用設計書」を基本とする。本章では、リスキーアラート作成システム固有の運用要件について記述する。

### 8.1 デプロイメント手順

#### 8.1.1 パイプライン実行によるデプロイ

**手動実行フロー**

1. **実行前準備**
   - Azure DevOpsポータルにサインインする
   - 対象パイプライン「terraform-risky-alert-deployment」を選択する
   - 実行対象の環境（dev/staging/prod）を確認する

2. **パラメータ入力**
   - 「Run pipeline」をクリックし、以下のパラメータを入力する：
     - environment: デプロイ対象環境
     - alert_name: アラート名（既存アラートの更新時は同一名を指定）
     - action: 実行アクション（create/update/delete）
     - risk_level: リスクレベル（low/medium/high）
     - threshold_count: 閾値
     - enabled: アラートの有効/無効

3. **実行と確認**
   - 「Run」ボタンをクリックして実行開始
   - Terraform Planの結果を確認（ドライラン）
   - 本番環境の場合は承認者による承認を待つ
   - Terraform Applyの実行結果を確認

**スケジュール実行の設定**

```yaml
schedules:
- cron: "0 2 * * 1"  # 毎週月曜日 AM 2:00 (JST: AM 11:00)
  displayName: Weekly Alert Rule Validation
  branches:
    include:
    - main
  parameters:
    environment: prod
    action: plan_only
```

#### 8.1.2 エラーハンドリング

**一般的なエラーと対処方法**

| エラー種別 | エラーメッセージ例 | 対処方法 |
|-----------|-------------------|----------|
| 認証エラー | "Error authenticating using Service Principal" | サービスプリンシパルの証明書期限を確認し、必要に応じて更新する |
| State Lockエラー | "Error acquiring the state lock" | 他のパイプラインが実行中でないか確認し、必要に応じてロックを手動解除する |
| API制限エラー | "Rate limit exceeded" | 15分待機後に再実行するか、実行を分割する |
| リソース競合 | "Resource already exists" | 既存リソースの状態を確認し、updateアクションで実行する |
| 検証エラー | "Invalid parameter value" | 入力パラメータを確認し、正しい値で再実行する |

**自動リトライ設定**

パイプライン内で一時的なエラーに対する自動リトライを実装する：

```yaml
- task: AzureCLI@2
  retryCountOnTaskFailure: 3
  inputs:
    azureSubscription: 'sc-azure-risky-alert-$(environment)'
    scriptType: 'bash'
    scriptLocation: 'inlineScript'
    inlineScript: |
      terraform apply -auto-approve
```

### 8.2 監視・アラート

**システム監視項目**

本システム固有の監視項目は以下の通りである。共通的な監視項目については、「自動プロビジョニングシステム運用設計書」第4章「監視設計」を参照すること。

1. **パイプライン実行監視**
   - 実行成功率：95%以上を維持
   - 平均実行時間：5分以内（警告）、10分以内（エラー）
   - 連続失敗回数：3回以上でアラート

2. **リソース状態監視**
   - 作成されたアラートルールの有効性
   - アラートルールの発火頻度（異常な頻度の検知）
   - Action Groupの到達性

3. **セキュリティ監視**
   - 未承認のパイプライン実行試行
   - サービスプリンシパルの異常な使用パターン
   - 本番環境への直接的な変更試行

**障害時の通知設定**

```json
{
  "alert_rules": [
    {
      "name": "pipeline-failure-alert",
      "condition": "Pipeline failed 3 times consecutively",
      "severity": "High",
      "notification": {
        "email": ["risky-alert-team@example.com"],
        "teams_webhook": "https://outlook.office.com/webhook/..."
      }
    },
    {
      "name": "deployment-duration-alert",
      "condition": "Deployment duration > 15 minutes",
      "severity": "Medium",
      "notification": {
        "email": ["risky-alert-team@example.com"]
      }
    }
  ]
}
```

### 8.3 バックアップ・リカバリ

#### 8.3.1 構成情報のバックアップ

**自動バックアップ対象**

1. **Terraformコード**
   - Gitリポジトリによる完全なバージョン管理
   - Azure DevOps Reposの地理冗長性により保護
   - 外部Gitリポジトリへの日次ミラーリング

2. **Terraform State**
   - Azure Storage AccountのGRSによる自動レプリケーション
   - Blob Storageのバージョニング機能により30世代を保持
   - 週次でのコールドストレージへのアーカイブ

3. **設定ファイル（config.json）**
   - リポジトリ内でのバージョン管理
   - 重要な変更時にはタグ付けを実施
   - 四半期ごとのスナップショット作成

**手動バックアップ手順**

緊急時や大規模変更前の手動バックアップ：

```bash
# 1. 現在のStateをエクスポート
terraform state pull > backup/terraform-state-$(date +%Y%m%d-%H%M%S).json

# 2. 設定ファイルのバックアップ
cp config.json backup/config-$(date +%Y%m%d-%H%M%S).json

# 3. バックアップファイルをセキュアな場所に保存
az storage blob upload-batch \
  --destination backup-container \
  --source ./backup \
  --account-name <storage-account>
```

#### 8.3.2 障害復旧手順

**軽微な障害（アラートルールの誤設定）**

1. 影響を受けたアラートルールを特定する
2. Gitリポジトリから正しい設定を取得する
3. updateアクションでパイプラインを再実行する
4. 動作確認を実施する

**中規模障害（State破損）**

1. 破損したStateファイルを隔離する
2. バックアップから最新の正常なStateを復元する：
   ```bash
   # バックアップからStateを復元
   az storage blob download \
     --container-name terraform-state-risky-alerts \
     --name terraform.tfstate.backup \
     --file terraform.tfstate
   ```
3. `terraform plan`で現在の状態との差分を確認する
4. 必要に応じて`terraform import`で既存リソースを再インポートする

**大規模障害（環境全体の再構築）**

大規模障害時の復旧手順については、「自動プロビジョニングシステム運用設計書」第6章「災害復旧計画」を参照すること。本システム固有の考慮事項：

- 復旧優先度：本番環境 → ステージング環境 → 開発環境
- 目標復旧時間（RTO）：4時間以内
- 目標復旧時点（RPO）：1時間以内
- 復旧後の全アラートルールの動作確認を必須とする順

