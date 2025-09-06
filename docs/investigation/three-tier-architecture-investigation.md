# Claude Code Three Tier Architecture POC 調査結果

## 概要

Claude CodeにおけるLayer 1（カスタムスラッシュコマンド）からLayer 2（キャラクターエージェント）を経由してLayer 3（サブエージェント）を呼び出す3層アーキテクチャの実現可能性を調査。

## 調査日時

2025-09-06

## 目標

```
Layer 1: Custom Slash Commands (/command-*)
  ↓
Layer 2: Character Agents (zundamon-agent, lum-agent)  
  ↓
Layer 3: Sub-agents (zundamon-greeter, zundamon-storyteller, etc.)
```

## 課題と解決アプローチ

### 課題1: Layer 1からLayer 2のカスタムスラッシュコマンドを呼び出せない

**問題**: 
- カスタムスラッシュコマンド（`.claude/commands/*.md`）はClaude Codeが直接実行するもの
- Taskツールでは呼び出すことができない

**解決アプローチ**: ハック的疑似実行方式
1. Layer 2専用のサブエージェント（`*-command-executor`）を作成
2. カスタムスラッシュコマンドのMarkdown内容を読み取り
3. その内容を専用サブエージェントにプロンプトとして渡す
4. サブエージェントがカスタムスラッシュコマンドの動作を疑似実行

### 課題2: Layer 2サブエージェントからLayer 3サブエージェントを呼び出せるか不明

**問題**:
- サブエージェント内でTaskツールを使って別のサブエージェントを呼び出せるかどうかの仕様が不明

**調査結果**: 要検証

## 実装したファイル構成

### Layer 1: Orchestrator
```
poml/commands/no-context/orchestrator.poml
```

### Layer 2: Command Executors
```
.claude/agents/no-context/zundamon-command-executor.md
.claude/agents/no-context/lum-command-executor.md
```

### Layer 3: Sub-agents（既存）
```
.claude/agents/no-context/zundamon-greeter.md
.claude/agents/no-context/zundamon-storyteller.md
.claude/agents/no-context/lum-greeter.md
.claude/agents/no-context/lum-emotion.md
```

## 実行フロー

### 修正前（2層実行）
```
Layer 1: /no-context:orchestrator
  ↓ 直接実行
Layer 3: zundamon-greeter, zundamon-storyteller, lum-greeter, lum-emotion
```

### 修正後（3層実行）
```
Layer 1: /no-context:orchestrator
  ↓ Task(subagent_type: "zundamon-command-executor")
Layer 2: zundamon-command-executor
  ↓ Task(subagent_type: "zundamon-greeter") ※要検証
Layer 3: zundamon-greeter, zundamon-storyteller
```

## 技術的な発見

### POML（Prompt Orchestration Markup Language）
- `npx pomljs --file <filename>` で実行
- XML様式でタスク定義が可能
- Claude Codeに実行指示を与えることができる

### カスタムスラッシュコマンド疑似実行
- Markdown内容を読み取ってプロンプト化
- 専用サブエージェントに渡すことで疑似実行を実現
- 完全なカスタムスラッシュコマンド実行ではないが、機能的に近似可能

## 検証結果

### ✅ 成功した項目
1. **POML解析**: pomljs正常動作
2. **Layer 1→Layer 2呼び出し**: Task toolでサブエージェント呼び出し成功
3. **疑似実行**: カスタムスラッシュコマンド内容の読み取りと実行指示の伝達
4. **キャラクター制御**: Zundamon・lum-chan両キャラクター対応

### ❓ 検証中の項目
1. **Layer 2→Layer 3呼び出し**: サブエージェントから別のサブエージェントのTask呼び出しの可否
2. **真の3層実行**: 完全な階層的実行の実現可能性

## 制限事項

### Claude Code仕様による制限
- カスタムスラッシュコマンドの直接呼び出し不可
- サブエージェント間のTask呼び出し可否が未確定
- メモリ使用量の制約（大量実行時のheap out of memory）

### 疑似実行の限界
- 真のカスタムスラッシュコマンド実行ではない
- マークダウン内容の解釈による近似実行
- Claude Code固有の機能（hooks等）は再現困難

## 今後の課題

### 技術検証
1. Layer 2サブエージェントからのTask呼び出し動作確認
2. メモリ使用量最適化
3. エラーハンドリング強化

### 真のハック手法検索
1. Claude Code CLI直接呼び出し方法の模索
2. プロセス間通信を使った実行方法
3. 代替アーキテクチャパターンの検討

## 結論

**現状**: 疑似3層アーキテクチャの部分的実現に成功

**評価**: 
- ハック的手法により設計意図の大部分を実現
- Layer 2→Layer 3呼び出しの完全検証が今後の鍵
- 実用性を持った3層アーキテクチャの基盤を構築

**次のステップ**: 
1. Layer 2→Layer 3呼び出しの動作確認
2. 完全実行テストの実施
3. 実用化に向けた最適化

---

*この調査は継続中であり、新たな発見があれば随時更新する予定です。*