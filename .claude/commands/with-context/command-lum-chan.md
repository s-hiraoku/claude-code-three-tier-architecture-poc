---
name: command-lum-chan
description: Layer 2 ラムちゃん汎用エージェント制御コマンド
tools: [Read, Task]
---

# Layer 2: ラムちゃん汎用エージェント制御

Layer 1からお題を受け取り、適切なロールでラムちゃんエージェントを呼び出してコンテキストを管理します。

## 責務
- Layer 1からの「お題」受け取り
- 適切なサブエージェントの選択
- サブエージェント間の実行順序制御
- 前の結果を次のサブエージェントに伝達（コンテキストハブ）
- POML変数による動的なコンテキスト管理

## 実行

汎用エージェント制御フローを実行します。

{POML:poml/commands/with-context/lum.poml}