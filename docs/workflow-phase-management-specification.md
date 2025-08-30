# ワークフローフェーズ管理仕様書

## 概要

Claude Code 3層アーキテクチャにおける**ワークフローフェーズ管理システム**の設計仕様です。
Layer 2でプロジェクトの各フェーズを管理し、フェーズごとに最適化された専門エージェント群を動的に実行します。

## 🎯 設計目的

- **フェーズ駆動開発**: プロジェクトの各段階に応じた専門的な処理
- **動的エージェント選択**: フェーズに基づく最適なエージェント群の自動選択
- **ワークフロー標準化**: 一貫したプロジェクト進行プロセス
- **拡張性**: 新しいフェーズ・エージェントの容易な追加

## 🏗️ アーキテクチャ設計

### Layer構成

```
┌─────────────────────────────────────────┐
│ Layer 1: Orchestrator                  │
│ - フェーズ選択と統合制御                   │
│ - /with-context:agent {phase}          │
└─────────────┬───────────────────────────┘
              │ フェーズ指定
┌─────────────▼───────────────────────────┐
│ Layer 2: Phase Management Commands     │
│ - フェーズ固有の処理制御                   │
│ - 専門エージェント群の組み合わせ実行        │
└─────────────┬───────────────────────────┘
              │ 専門エージェント実行
┌─────────────▼───────────────────────────┐
│ Layer 3: Specialized Agents            │
│ - フェーズ特化型エージェント               │
│ - 具体的な専門処理実行                   │
└─────────────────────────────────────────┘
```

## 📋 フェーズ分類

### 1. 仕様検討フェーズ (specification-phase)
**目的**: 要件定義と仕様策定
**専門エージェント**:
- `overview-designer`: 全体設計・アーキテクチャ検討
- `researcher`: 技術調査・市場分析
- `analyst`: 要件分析・課題整理

### 2. 設計フェーズ (design-phase)
**目的**: 詳細設計とアーキテクチャ設計
**専門エージェント**:
- `architect`: システムアーキテクチャ設計
- `ui-designer`: UI/UX設計
- `database-designer`: データベース設計

### 3. 開発フェーズ (development-phase)
**目的**: 実装とコーディング
**専門エージェント**:
- `developer`: コード実装
- `reviewer`: コードレビュー
- `refactor`: リファクタリング

### 4. テストフェーズ (testing-phase)
**目的**: 品質保証とテスト
**専門エージェント**:
- `tester`: テストケース作成・実行
- `debugger`: バグ修正
- `validator`: 品質検証

## ⚙️ ファイル構成

### Layer 2: フェーズ管理POML
```
poml/commands/with-context/
├── specification-phase.poml    # 仕様検討フェーズ制御
├── design-phase.poml          # 設計フェーズ制御
├── development-phase.poml     # 開発フェーズ制御
└── testing-phase.poml         # テストフェーズ制御
```

### Layer 3: 専門エージェント
```
poml/agents/with-context/
├── overview-designer-behavior.poml
├── researcher-behavior.poml
├── analyst-behavior.poml
├── architect-behavior.poml
├── ui-designer-behavior.poml
├── database-designer-behavior.poml
├── developer-behavior.poml
├── reviewer-behavior.poml
├── refactor-behavior.poml
├── tester-behavior.poml
├── debugger-behavior.poml
└── validator-behavior.poml
```

## 🔄 実行フロー

### コマンド実行例
```bash
# 仕様検討フェーズの実行
/with-context:agent specification-phase

# 設計フェーズの実行  
/with-context:agent design-phase

# 開発フェーズの実行
/with-context:agent development-phase

# テストフェーズの実行
/with-context:agent testing-phase
```

### フェーズPOMLの基本構造
```xml
<poml>
  <meta minVersion="0.0.8" />
  
  <role>仕様検討フェーズ制御エージェント</role>
  
  <task>
    コンテキスト: {{user_input}}
    
    仕様検討フェーズを実行：
    <for items="['overview-designer', 'researcher', 'analyst']" item="agent">
      <list>
        <item>専門エージェント {{agent}} を実行</item>
        <item>{{agent}} の結果をコンテキストに蓄積</item>
      </list>
    </for>
  </task>
</poml>
```

## 📐 設計原則

### フェーズ独立性
- 各フェーズは独立して実行可能
- フェーズ間の依存関係は最小限に抑制
- 前フェーズの結果は accumulated_results で管理

### エージェント特化性
- 各エージェントは特定の専門領域に特化
- 汎用性より専門性を重視
- フェーズ横断的なエージェントは避ける

### 動的拡張性
- 新フェーズの追加は新POMLファイル作成のみ
- 新エージェントの追加は behavior.poml 作成のみ
- 既存システムへの影響を最小化

## 🛠️ 実装ガイドライン

### フェーズPOML作成時
1. **明確な責務定義**: フェーズの目的と成果物を明記
2. **適切なエージェント選択**: フェーズに最適化されたエージェント組み合わせ
3. **結果統合**: 各エージェントの成果をコンテキストに統合

### エージェントbehavior.poml作成時
1. **専門性の確保**: 特定領域に特化した処理内容
2. **入力形式の統一**: user_input からの情報取得方法を統一
3. **出力品質**: 次工程で利用可能な構造化された出力

## 📊 期待効果

### 開発効率向上
- **フェーズ別最適化**: 各段階に最適な専門エージェント活用
- **プロセス標準化**: 一貫したワークフロー実行
- **並行処理**: フェーズ内エージェントの効率的実行

### 品質向上
- **専門性活用**: 各領域のエキスパートエージェント活用
- **段階的検証**: フェーズごとの成果物検証
- **継続的改善**: フェーズ結果の蓄積と活用

### 保守性向上
- **モジュール化**: フェーズ・エージェントの独立性
- **拡張容易性**: 新要素の追加コスト最小化
- **再利用性**: エージェントの他フェーズでの再利用

## 🎯 適用シナリオ

### プロジェクト開始時
```bash
# 要件定義から開始
/with-context:agent specification-phase "新ECサイト開発"
```

### 設計段階
```bash
# 仕様確定後の詳細設計
/with-context:agent design-phase "ECサイト詳細設計"
```

### 開発段階  
```bash
# 設計に基づく実装
/with-context:agent development-phase "ECサイト実装"
```

### 品質保証段階
```bash
# 実装完了後のテスト
/with-context:agent testing-phase "ECサイトテスト"
```

## 📚 関連ドキュメント

- [Claude Code 3層アーキテクチャ実装指針](./architecture-guide.md)
- [コンテキスト受け渡し仕様書](./context-passing-specification.md)
- [POML Multiple Task Elements Documentation](./poml-multiple-task-elements.md)

---

**作成日**: 2025-01-30  
**バージョン**: v1.0  
**ステータス**: ドラフト

この仕様書は、Claude Code Three Tier Architecture POCにおける
**ワークフローフェーズ管理システム**の設計概念を定義します。