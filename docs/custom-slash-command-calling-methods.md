# カスタムスラッシュコマンド間呼び出し方法

## 概要

Claude Codeにおけるカスタムスラッシュコマンドから別のカスタムスラッシュコマンドを呼び出す方法の検証結果と確立された手法。

## 結論: 実用的な呼び出し方法

### File-based Coordination (ファイルベース協調)

**唯一の実用的な方法**として確立された手法：

```markdown
### Method 1: File-based Coordination
Instead of calling slash commands directly, I'll:
1. Read the .md files of target commands
2. Execute their instructions directly  
3. Simulate the command execution
```

## 実装パターン

### 親コマンドでの記述方法

```markdown
---
name: parent-command
description: Command that coordinates other custom slash commands
tools: [Read, Bash, Edit, Write]
---

# Parent Command

## My Task

I need to coordinate multiple specialized commands:
1. Call `/namespace:target-command-1` for specific processing
2. Call `/namespace:target-command-2` for additional processing
3. Integrate the results from both commands

## Attempted Methods

### Method 1: File-based Coordination
Instead of calling slash commands directly, I'll:
1. Read the .md files of target commands
2. Execute their instructions directly
3. Simulate the command execution

Let me try Method 1 first - reading and executing command files directly...
```

### Claude Codeによる自動実行

上記の指示により、Claude Codeは以下を自動実行：

1. **Read tool**: `.claude/commands/{namespace}/{target-command}.md` を読み込み
2. **直接実行**: 読み込んだファイルの指示に従って処理を実行
3. **POML実行**: 必要に応じて `npx pomljs --file poml/commands/{namespace}/{target-command}.poml` を実行
4. **結果統合**: 各コマンドの実行結果を統合

## 検証済み実例

### 実行コマンド
```bash
claude -p "/no-context:parent-command"
```

### 実行結果
- ✅ `/no-context:zundamon` コマンドの処理が実行
- ✅ `/no-context:lum-chan` コマンドの処理が実行
- ✅ 4つのサブエージェント（Layer 3）が呼び出された
- ✅ 全結果が統合されて表示

### 実際の実行フロー
```
parent-command.md (Layer 1)
├── zundamon.md の内容実行 (Layer 2)
│   ├── zundamon-greeter 呼び出し (Layer 3)
│   └── zundamon-storyteller 呼び出し (Layer 3)
└── lum-chan.md の内容実行 (Layer 2)
    ├── lum-greeter 呼び出し (Layer 3)
    └── lum-emotion 呼び出し (Layer 3)
```

## 禁止される方法（検証済み失敗パターン）

### ❌ 直接スラッシュコマンド呼び出し
```bash
# セッション競合により失敗
claude -p "/namespace:target-command"
```

### ❌ Taskツールでの呼び出し
```javascript  
// カスタムスラッシュコマンドはサブエージェントではないため失敗
Task({
  subagent_type: "command-name",
  description: "...",
  prompt: "..."
})
```

## 設計上の利点

1. **セッション競合回避**: 直接呼び出しによる競合問題を回避
2. **3層アーキテクチャ維持**: Layer 1 → Layer 2 → Layer 3 の構造を保持
3. **実装の簡潔性**: 複雑な技術的ハックが不要
4. **保守性**: 各コマンドファイルの独立性を維持
5. **可読性**: 意図が明確で理解しやすい実装

## 適用ガイドライン

1. **親コマンドに意図を記述**: "Call" や "Execute" などの抽象的な表現でOK
2. **Method 1パターンを明記**: File-based Coordinationの手順を記載
3. **Claude Codeに実行を委託**: 具体的なRead/Bash toolの呼び出しは自動実行される
4. **結果統合を指示**: 各コマンドの結果を統合する旨を記載

この方法により、カスタムスラッシュコマンド間の協調動作が確実に実現できる。