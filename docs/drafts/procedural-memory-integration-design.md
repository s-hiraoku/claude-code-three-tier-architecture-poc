# Procedural Memory Integration Design for Three Tier Architecture

## 概要

本ドキュメントは、Claude Code Three Tier Architecture POC に手続き記憶（Procedural Memory）システムを統合する設計書です。論文「Mem[p]: Exploring Agent Procedural Memory」の知見を基に、既存の3層アーキテクチャに記憶層を追加し、エージェントの学習・改善機能を実装します。

**参考論文**: [Mem[p]: Exploring Agent Procedural Memory](https://arxiv.org/abs/2501.10615)

## 背景

### 手続き記憶とは

手続き記憶（Procedural Memory）は、タスクの実行方法やスキルを記憶する長期記憶の一種です。人間が自転車の乗り方を覚えているように、エージェントも過去の成功体験から学習し、同様のタスクを効率的に実行できるようになります。

### 既存システムの課題

現在の3層アーキテクチャでは：
- 各タスクを毎回最初から実行
- 過去の成功体験が活用されない
- 同種のタスクでも同じ試行錯誤を繰り返す
- 実行効率とトークン消費が非効率

## アーキテクチャ設計

### 拡張後の4層構造

```
┌─────────────────────────────────────────┐
│ Layer 0: Procedural Memory System      │
│ - 軌跡の記憶構築（Build）                 │
│ - 類似記憶検索（Retrieve）                │
│ - 動的記憶更新（Update）                  │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Layer 1: Memory-Aware Orchestrator     │
│ - 記憶システムとの連携                    │
│ - 過去の経験を考慮したフロー管理           │
│ - 記憶の蓄積と利用の調整                  │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Layer 2: Enhanced Custom Commands      │
│ - 記憶検索による処理最適化                │
│ - 成功パターンの再利用                    │
│ - 失敗からの学習機能                      │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Layer 3: Learning Sub-Agents           │
│ - 実行結果の記憶システムへの反映           │
│ - 個別エージェントレベルでの学習           │
│ - 専門分野での手続き記憶蓄積              │
└─────────────────────────────────────────┘
```

### 記憶システムの3要素

#### 1. Build（記憶構築）
- **Trajectory Storage**: 実行軌跡をそのまま保存
- **Script Abstraction**: 軌跡から高レベルな手順を抽出
- **Proceduralization**: 具体例と抽象化を組み合わせ

#### 2. Retrieve（記憶検索）
- **Query-based**: タスクの意味的類似度による検索
- **Keyword-based**: 重要キーワードの平均類似度
- **Vector Embedding**: 高次元ベクトル空間での検索

#### 3. Update（記憶更新）
- **Validation**: 成功したタスクのみを記憶として保存
- **Reflection**: 失敗時に既存記憶を修正・改善
- **Dynamic Pruning**: 古い・無効な記憶の自動削除

## ファイル構成

### 新規追加ディレクトリ構造

```
poml/
├── memory/                              # Layer 0: 記憶システム
│   ├── procedural-memory-manager.poml   # 記憶システム統括管理
│   ├── memory-builder.poml              # 軌跡から記憶構築
│   ├── memory-retriever.poml            # 類似記憶検索
│   └── memory-updater.poml              # 動的記憶更新
├── storage/                             # 記憶データ保存
│   ├── trajectories/                    # 実行軌跡保存
│   │   ├── successful/                  # 成功軌跡
│   │   └── failed/                      # 失敗軌跡（学習用）
│   ├── abstractions/                    # 抽象化スクリプト
│   │   ├── patterns/                    # パターン化された手順
│   │   └── templates/                   # テンプレート
│   └── embeddings/                      # ベクトル表現
│       ├── task_embeddings.json        # タスク埋め込み
│       └── similarity_index.json       # 類似度インデックス
└── context/
    ├── context.poml                     # 既存コンテキスト（拡張）
    └── memory-context.poml              # 記憶システム専用コンテキスト
```

### 既存ファイルの拡張

#### context.poml 拡張項目
```xml
<!-- 手続き記憶関連の追加項目 -->
<let name="memory_enabled" value="true"/>
<let name="current_trajectory" value="[]"/>
<let name="retrieved_memories" value="[]"/>
<let name="task_similarity_threshold" value="0.75"/>
<let name="memory_update_mode" value="validation"/>
```

## 実装段階

### Phase 1: 基本記憶機能
**目標**: 軌跡保存と基本検索
- 実行軌跡の JSON 形式保存
- 単純な文字列マッチング検索
- 成功/失敗の基本分類

**実装期間**: 1-2週間

### Phase 2: 類似度ベース検索
**目標**: セマンティック検索の実装
- ベクトル埋め込みによる類似度計算
- コサイン類似度ベースの検索
- 検索結果のランキング機能

**実装期間**: 2-3週間

### Phase 3: 記憶抽象化機能
**目標**: 軌跡からのパターン抽出
- 実行手順の自動抽象化
- テンプレート生成機能
- Proceduralization（具体例+抽象化）

**実装期間**: 3-4週間

### Phase 4: 動的更新システム
**目標**: 学習・改善機能
- 失敗からの反省的学習
- 記憶の動的修正・更新
- 古い記憶の自動廃棄

**実装期間**: 4-6週間

### Phase 5: 転移学習機能
**目標**: エージェント間での記憶共有
- 記憶の他エージェントへの移行
- 弱いモデルへの知識転移
- 記憶品質の評価・最適化

**実装期間**: 3-4週間

## POML実装例

### procedural-memory-manager.poml
```xml
<poml>
  <meta minVersion="0.0.8" />
  
  <role>手続き記憶システム統括管理エージェント</role>
  
  <task>
    記憶システム初期化：
    <list>
      <item>現在のタスクコンテキストを分析</item>
      <item>類似する過去の記憶を検索</item>
      <item>検索結果をランキングして上位3件を選択</item>
      <item>選択した記憶をコンテキストに統合</item>
    </list>
  </task>
  
  <task>
    記憶構築・更新：
    <list>
      <item>実行完了後の軌跡を分析</item>
      <item>成功/失敗の判定</item>
      <item>成功時: 新しい記憶として保存</item>
      <item>失敗時: 既存記憶の修正または削除判定</item>
    </list>
  </task>
</poml>
```

### memory-builder.poml
```xml
<poml>
  <meta minVersion="0.0.8" />
  
  <role>実行軌跡から手続き記憶を構築するエージェント</role>
  
  <task>
    軌跡分析と記憶構築：
    <list>
      <item>実行軌跡の各ステップを分析</item>
      <item>成功に寄与した重要なアクションを特定</item>
      <item>具体的な実行手順を抽象化</item>
      <item>再利用可能なパターンとして記憶化</item>
    </list>
  </task>
  
  <output-format>
    {
      "memory_id": "mem_{{timestamp}}_{{task_hash}}",
      "task_description": "{{task_context}}",
      "trajectory": {{execution_steps}},
      "abstraction": "{{high_level_steps}}",
      "success_rate": {{success_probability}},
      "reusability_score": {{reusability_rating}}
    }
  </output-format>
</poml>
```

## 技術仕様

### 記憶データフォーマット

#### 軌跡記録形式
```json
{
  "memory_id": "mem_20250130_task001",
  "timestamp": "2025-01-30T12:00:00Z",
  "task_type": "conversation",
  "task_description": "天気について議論",
  "participants": ["zundamon", "lum"],
  "trajectory": [
    {
      "step": 1,
      "agent": "zundamon",
      "action": "weather_information_search",
      "result": "success",
      "tokens_used": 150
    }
  ],
  "final_result": "success",
  "total_steps": 3,
  "total_tokens": 450,
  "abstraction": "天気議論のための情報収集→専門知識提供→創意的応答"
}
```

#### 類似度インデックス
```json
{
  "task_embeddings": {
    "mem_20250130_task001": [0.1, 0.8, -0.3, ...],
    "mem_20250130_task002": [0.2, 0.7, -0.1, ...]
  },
  "similarity_cache": {
    "mem_20250130_task001": {
      "mem_20250130_task002": 0.85,
      "mem_20250129_task003": 0.62
    }
  }
}
```

## 期待される効果

### 性能向上指標
- **成功率向上**: 類似タスクで20-40%の成功率改善
- **実行効率化**: 平均ステップ数を30-50%削減
- **トークン削減**: 平均トークン消費を25-35%削減
- **学習曲線**: 継続的な性能向上（線形改善）

### システムメリット
- **継続学習**: エージェントの自動改善
- **知識蓄積**: 組織レベルでの知見共有
- **転移学習**: 新しいタスクへの知識適用
- **効率化**: 繰り返し作業の自動化

## リスク管理

### 技術的リスク
- **記憶品質**: 不正確な記憶による性能劣化
- **検索精度**: 不適切な類似記憶の選択
- **データ肥大**: 記憶データの指数的増加

### 対策
- **バリデーション機能**: 記憶の品質評価
- **定期クリーンアップ**: 古い記憶の自動削除
- **A/Bテスト**: 記憶システムありなしの比較

## 今後の発展

### 拡張可能性
- **多モーダル記憶**: テキスト以外の記憶形式
- **分散記憶**: 複数エージェント間での記憶共有
- **メタ学習**: 学習方法自体の最適化

### 研究課題
- **記憶の忘却**: 人間的な忘却機能の実装
- **創発的学習**: 予期しないパターンの発見
- **倫理的記憶**: 適切でない記憶の自動除外

## 結論

手続き記憶システムの統合により、Claude Code Three Tier Architecture POC は単なるタスク実行システムから、学習・改善機能を持つ知的システムへと進化します。段階的な実装アプローチにより、リスクを最小化しながら確実な機能追加が可能です。