---
name: command-zundamon
description: Layer 2 ずんだもん汎用エージェント制御コマンド
tools: [Read, Task]
---

# Layer 2: ずんだもん汎用エージェント制御

Layer 1からお題を受け取り、適切なロールでずんだもんエージェントを呼び出してコンテキストを管理します。

## 責務
- Layer 1からの「お題」受け取り
- 適切なサブエージェントの選択
- サブエージェント間の実行順序制御
- POML変数による動的なコンテキスト管理

## 実行

汎用エージェント制御フローを実行します。

{POML:poml/commands/with-context/zundamon.poml}