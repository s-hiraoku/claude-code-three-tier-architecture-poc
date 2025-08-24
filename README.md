# Claude Code Three Tier Architecture POC

## 概要

Claude Code Three Tier Architecture POC は、Claude Code の機能を活用した **3 層アーキテクチャによるオーケストレーション基盤** の実装例です。

汎用的な実装指針により、様々な開発ワークフローに適用可能な拡張性の高いアーキテクチャを提供します。

## 🏗️ アーキテクチャ構成

```
┌─────────────────────────────────────────┐
│ Layer 1: Orchestrator                  │
│ - POMLファイルによる動作制御             │
│ - マークダウンファイル読み込み＆実行      │
│ - 全体フロー管理                        │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Layer 2: Custom Slash Commands         │
│ - マークダウンファイル（.md）として定義  │
│ - 各専門領域の処理ロジック               │
│ - サブエージェント選択・呼び出し         │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Layer 3: Sub-Agents                    │
│ - Taskツールで実行される専門エージェント │
│ - 実際の作業実行（挨拶、物語、感情表現等）│
│ - POMLファイルで動作定義                │
└─────────────────────────────────────────┘
```

## 📁 ファイル構成

### 現在の実装例（キャラクターエージェント）

```
.claude/
├── commands/
│   ├── command-zundamon.md          # ずんだもんコマンド定義
│   ├── command-lum-chan.md          # ラムちゃんコマンド定義
│   └── orchestrator.md              # オーケストレーター定義
└── agents/
    ├── zundamon-greeter.md          # ずんだもん挨拶サブエージェント
    ├── zundamon-storyteller.md      # ずんだもん物語サブエージェント
    ├── lum-greeter.md               # ラムちゃん挨拶サブエージェント
    └── lum-emotion.md               # ラムちゃん感情サブエージェント

poml/
├── commands/
│   ├── orchestrator.poml            # オーケストレーター動作制御
│   ├── zundamon.poml                # ずんだもんコマンド動作制御
│   └── lum.poml                     # ラムちゃんコマンド動作制御
└── agents/
    ├── zundamon-greeter-behavior.poml      # ずんだもん挨拶動作定義
    ├── zundamon-storyteller-behavior.poml # ずんだもん物語動作定義
    ├── lum-greeter-behavior.poml          # ラムちゃん挨拶動作定義
    └── lum-emotion-behavior.poml          # ラムちゃん感情動作定義
```

## 🚀 実行方法

### 基本実行

```bash
/orchestrator
```

### 個別コマンド実行

```bash
/zundamon    # ずんだもんキャラクターコマンド
/lum-chan    # ラムちゃんキャラクターコマンド
```

## ⚡ 実行フロー

1. **Layer 1**: Orchestrator が POML ファイルを読み込み、実行順序を決定
2. **Layer 2**: 各キャラクターコマンドが POML ファイルに基づいてサブエージェントを選択
3. **Layer 3**: 専門サブエージェントが具体的なタスク（挨拶、物語生成、感情表現）を実行

## 🎯 設計の特徴

- **層間疎結合**: 各層が独立しており、変更の影響を最小化
- **動的動作決定**: POML ファイルによる実行時動作決定
- **責務明確化**: 各層が単一責任を持ち、メンテナンス性が高い
- **再利用性**: テンプレート化により、異なるドメインで再利用可能
- **拡張性**: 新しいサブエージェントやコマンドの追加が容易

## 📚 ドキュメント

詳細な実装指針とガイダンスは以下を参照してください：

- [実装指針（日本語）](docs/architecture-guide.md)
- [Implementation Guide (English)](docs/architecture-guide-en.md)

## 🛠️ 適用方法

この POC は汎用的な実装指針として設計されており、以下の手順で任意のワークフローに適用できます：

1. ワークフロー分類の定義
2. 名前空間の決定
3. 必要なサブエージェントの設計
4. 専門コマンドの作成
5. オーケストレーターの定義

詳細は [docs/architecture-guide.md](docs/architecture-guide.md) を参照してください。
