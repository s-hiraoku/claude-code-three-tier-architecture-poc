# 3層アーキテクチャ最終設計仕様

## 概要

Claude Code Three Tier Architecture POCの検証結果に基づく、正確な3層アーキテクチャ設計仕様書。

## アーキテクチャ原則

### 中央集権型オーケストレーション

```
Layer 1: Orchestrator (オーケストレーター)
├── Layer 2A: Workflow A実行 → Layer 1に結果返却
├── Layer 2B: Workflow B実行 → Layer 1に結果返却  
├── Layer 2C: Workflow C実行 → Layer 1に結果返却
└── 全結果の統合・分析
```

**重要**: Layer 2間の直接通信は存在しない。すべてLayer 1が中央制御する。

## 各層の責務

### Layer 1: Orchestrator
- **役割**: 真の指揮者・調整役
- **責務**: 
  - 複数ワークフローの順序決定
  - 各Layer 2からの結果受け取り
  - 全体結果の統合・分析
  - 最終アウトプットの生成
- **制約**: 実装詳細には関与しない

### Layer 2: Workflow Commands  
- **役割**: 独立したワークフロー実行器
- **責務**:
  - 専門領域のワークフロー実行
  - 必要に応じてLayer 3サブエージェント呼び出し
  - Layer 1への結果返却
- **制約**: 他のLayer 2との直接通信禁止

### Layer 3: Sub-Agents
- **役割**: 原子的タスク実行器
- **責務**:
  - 具体的な作業実行
  - Layer 2からの指示に従った処理
- **制約**: 他層との直接通信なし

## 通信パターン

### 検証済みパターン
- **Layer 1 → Layer 2**: Readツールで.mdファイル読み込み、内容直接実行
- **Layer 2 → Layer 1**: ワークフロー完了時の結果返却
- **Layer 2 → Layer 3**: Taskツールによるサブエージェント呼び出し

### 禁止パターン
- ❌ Layer 2 → Layer 2: 直接通信
- ❌ `claude -p`による スラッシュコマンド呼び出し
- ❌ Layer 2に対するTaskツール使用

## 実装例

### Layer 1実装例
```javascript
// orchestrator.md
1. Read(".claude/commands/namespace/workflow-a.md")
2. Execute workflow-a instructions directly  
3. Collect workflow-a results
4. Read(".claude/commands/namespace/workflow-b.md")
5. Execute workflow-b instructions directly
6. Collect workflow-b results
7. Integrate all results
```

### Layer 2実装例
```javascript
// workflow-a.md
1. Execute domain-specific processing
2. Task({subagent_type: "specialized-agent", ...}) // Layer 3呼び出し
3. Return results to Layer 1
```

## 設計の利点

1. **明確な責務分離**: 各層の役割が明確
2. **中央制御**: Layer 1による統一的なフロー管理
3. **独立性**: Layer 2ワークフローの独立実行
4. **スケーラビリティ**: 新しいワークフローの追加が容易
5. **保守性**: 各層の変更が他層に影響しない

## 適用パターン

### 従来の問題（300行orchestrator）
```
monolithic-orchestrator.md (300+ lines)
├── workflow-a logic (80 lines)
├── workflow-b logic (90 lines)  
├── workflow-c logic (70 lines)
└── integration logic (60 lines)
```

### 3層アーキテクチャ解決策
```
Layer 1: orchestrator.md (80 lines)
├── coordination logic only

Layer 2: 
├── workflow-a.md (50 lines)
├── workflow-b.md (60 lines)  
└── workflow-c.md (45 lines)

Layer 3: existing sub-agents
```

**結果**: 300行→80行 (73%削減) + モジュール化

## 検証状況

- ✅ Layer 1 → Layer 2 通信
- ✅ Layer 2 → Layer 3 通信  
- ✅ Layer 2の独立実行
- ✅ 中央集権型オーケストレーション
- ✅ コンテキスト管理
- ✅ POMLベース動作制御

この設計により、複雑なワークフローを管理可能な3層構造に分離し、保守性とスケーラビリティを実現する。