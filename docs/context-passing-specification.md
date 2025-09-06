# Claude Code 3 層アーキテクチャ 汎用コンテキスト受け渡し仕様書

## 概要

Claude Code Three Tier Architecture POC における**汎用コンテキスト受け渡し機能**の設計仕様です。
一つのコンテキストオブジェクトを全ての層で汎用的に受け渡すメタプロジェクトとしての概念実装を定義します。

## 🎯 設計目的

- **シングルコンテキスト**: 一つのコンテキストが全層を通して受け渡される
- **汎用性**: 任意のワークフローに適用可能な抽象的パターン
- **シンプル性**: POML 標準機能（`<let>`, `{{ }}`）のみを使用
- **層間独立性**: 各層がコンテキスト内部構造に依存しない設計

## 🏗️ アーキテクチャ設計

### コンテキスト受け渡しフロー

```
┌─────────────────────────────────────────┐
│ Layer 1: Orchestrator                  │
│ - コンテキスト初期化                      │
│ - context = { user_input, timestamp }   │
└─────────────┬───────────────────────────┘
              │ context 受け渡し
┌─────────────▼───────────────────────────┐
│ Layer 2: Custom Slash Commands         │
│ - コンテキスト拡張（オプション）           │
│ - context.workflow_data 追加            │
└─────────────┬───────────────────────────┘
              │ Layer3がサブエージェントの場合：Claude Codeの自然なコンテキスト機能を活用
              │ （構造化コンテキストではなく、会話履歴による情報伝達）
┌─────────────▼───────────────────────────┐
│ Layer 3: Sub-Agents                    │
│ - コンテキスト利用                       │
│ - context.user_input で処理実行         │
└─────────────────────────────────────────┘
```

## 📋 コンテキスト仕様

### 汎用コンテキスト構造

```javascript
context = {
  // 必須フィールド（全ワークフロー共通）
  user_input: string, // ユーザーからの入力内容
  timestamp: string, // 実行開始時刻
  execution_id: string, // 一意の実行識別子

  // 動的フィールド（ワークフロー固有）
  workflow_data: object, // ワークフロー特有のデータ
  accumulated_results: [], // 累積実行結果

  // メタ情報
  current_layer: string, // "layer_1" | "layer_2" | "layer_3"
  parent_layer_result: any, // 前の層からの結果
};
```

### フィールド詳細

| フィールド            | 型     | 必須 | 説明                               |
| --------------------- | ------ | ---- | ---------------------------------- |
| `user_input`          | string | ✅   | ユーザーからの入力。全層で参照可能 |
| `timestamp`           | string | ✅   | 処理開始時刻。トレーサビリティ用   |
| `execution_id`        | string | ✅   | 実行を一意に識別する ID            |
| `workflow_data`       | object | ❌   | ワークフロー固有の追加データ       |
| `accumulated_results` | array  | ❌   | 各層での実行結果を蓄積             |
| `current_layer`       | string | ❌   | 現在実行中の層を示すメタ情報       |
| `parent_layer_result` | any    | ❌   | 直前の層からの結果                 |

## ⚙️ POML 実装パターン

### Layer 1: Orchestrator パターン

```xml
<poml>
  <!-- コンテキスト初期化 -->
  <let name="context">{{
    user_input: user_request,
    timestamp: current_time,
    execution_id: unique_id,
    current_layer: "layer_1"
  }}</let>

  <role>オーケストレーター</role>
  <task>
    コンテキスト {{ context }} を使用して、
    Layer 2 コマンドを順次実行し、結果を統合する
  </task>
</poml>
```

### Layer 2: Commands パターン

```xml
<poml>
  <!-- コンテキスト継承・拡張 -->
  <let name="context">{{
    ...inherited_context,
    workflow_data: command_specific_data,
    current_layer: "layer_2",
    parent_layer_result: layer1_result
  }}</let>

  <role>専門コマンド制御</role>
  <task>
    継承コンテキスト {{ context }} を使用して、
    適切なサブエージェントを選択・実行する
  </task>
</poml>
```

### Layer 3: Sub-Agents パターン

```xml
<poml>
  <!-- コンテキスト受け取り・利用 -->
  <let name="context">{{ received_context }}</let>

  <role>{{ context.workflow_data.agent_role }}として動作</role>
  <task>
    ユーザー要求「{{ context.user_input }}」に基づいて、
    専門的な処理を実行する
  </task>

  <example>
    Input: {{ context.user_input }}
    Output: [処理結果をcontextに基づいて生成]
  </example>
</poml>
```

## 🔄 実装フロー仕様

### 1. コンテキスト初期化（Layer 1）

```
1. ユーザー入力を受け取る
2. 基本コンテキストを作成
3. Layer 2 に context を渡して実行
```

### 2. コンテキスト拡張（Layer 2）

```
1. Layer 1 から context を受け取る
2. 必要に応じてワークフロー固有データを追加
3. Layer 3 に拡張された context を渡して実行
```

### 3. コンテキスト利用（Layer 3）

```
1. Layer 2 から context を受け取る
2. context.user_input 等を利用して処理実行
3. 結果を Layer 2 に返却
```

## 📐 設計原則

### 汎用性の原則

1. **抽象化レベル**: 具体的な実装に依存しない高レベルパターン
2. **拡張性**: 新しいフィールドの追加が容易
3. **後方互換性**: 既存の実装を破壊しない

### 層間独立性の原則

1. **疎結合**: 各層がコンテキスト内部構造の詳細を知る必要がない
2. **単一責任**: 各層は自身の責任範囲のみでコンテキストを操作
3. **予測可能性**: コンテキスト変更が他層に与える影響を最小化

### シンプル性の原則

1. **標準機能のみ**: POML 標準の `<let>` と `{{ }}` のみ使用
2. **最小限の構造**: 必要最小限のフィールドで構成
3. **理解容易性**: 開発者が直感的に理解できる設計

## 🛠️ 実装ガイドライン

### DO（推奨事項）

- ✅ 必須フィールド（user_input, timestamp, execution_id）は常に含める
- ✅ コンテキスト拡張時は既存フィールドを保持する
- ✅ 層固有の処理は workflow_data 内に格納する
- ✅ エラー情報もコンテキスト内で管理する

### DON'T（禁止事項）

- ❌ カスタム POML タグは使用しない
- ❌ 必須フィールドを削除・変更しない
- ❌ 層を跨いでコンテキスト構造に依存した実装をしない
- ❌ コンテキスト内に機密情報を含めない

## 🔍 検証可能な要素

### 機能要求

1. **コンテキスト伝播**: Layer 1 → Layer 2 → Layer 3 の受け渡し
2. **情報保持**: user_input が全層で参照可能
3. **拡張性**: workflow_data で層固有情報を追加可能
4. **結果統合**: accumulated_results で各層の結果を蓄積

### 非機能要求

1. **パフォーマンス**: コンテキスト受け渡しによるオーバーヘッド最小化
2. **保守性**: 新しいワークフローへの適用が容易
3. **デバッグ性**: execution_id によるトレーサビリティ
4. **テスト容易性**: 各層で独立したコンテキスト検証が可能

## 🎯 適用方法

### 新規ワークフローへの適用手順

1. **コンテキスト設計**: 必要なフィールドを workflow_data で定義
2. **Layer 1 実装**: コンテキスト初期化ロジックを作成
3. **Layer 2 実装**: コンテキスト拡張・サブエージェント選択ロジックを作成
4. **Layer 3 実装**: コンテキスト利用・具体処理ロジックを作成
5. **検証**: 各層でのコンテキスト受け渡しを確認

### テンプレート活用

本仕様に基づいたテンプレートファイルを提供し、
新しいワークフローでの迅速な実装を支援します。

## 📚 関連ドキュメント

- [Claude Code 3 層アーキテクチャ実装指針](./architecture-guide.md)
- [POML 仕様書](https://docs.microsoft.com/poml)
- [実装例とサンプルコード](../examples/)

---

この仕様書は、Claude Code Three Tier Architecture POC における
**汎用コンテキスト受け渡し機能**のメタプロジェクトとしての設計概念を定義します。
