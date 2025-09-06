# カスタムスラッシュコマンド間呼び出し手法 - 完全ガイド

## 概要

Claude Codeにおいて、カスタムスラッシュコマンドから別のカスタムスラッシュコマンドを呼び出すための手法とその実装方法をまとめています。これまで直接的な方法は公式には提供されていませんでしたが、複数のハック的手法が発見されました。

## 🎯 問題設定

```
目標: Layer 1 → Layer 2 → Layer 3 の3層アーキテクチャ実現

Layer 1: /orchestrator (カスタムスラッシュコマンド)
  ↓ 呼び出し
Layer 2: /command-zundamon, /command-lum-chan (カスタムスラッシュコマンド)
  ↓ Task呼び出し  
Layer 3: zundamon-greeter, zundamon-storyteller (サブエージェント)
```

**課題**: Claude Codeには直接的なスラッシュコマンド間呼び出しAPIが存在しない

## 🔍 発見した手法

### 手法1: Bash コマンド埋め込み方式 ⭐ **推奨**

#### 原理
カスタムスラッシュコマンドのMarkdownファイル内で `!` を使用してbashコマンドを実行し、その中でClaude CLIを使って別のスラッシュコマンドを呼び出す。

#### 実装方法
```markdown
---
name: orchestrator
description: Master orchestrator that calls other slash commands
tools: [Bash, Read]
---

# Master Orchestrator

## Layer 2 Command Execution

### Zundamon Command Call:
!timeout 30s claude -p --session-id "zundamon-$(date +%s)" "/command-zundamon" 2>&1 || echo "FAILED: /command-zundamon"

### lum-chan Command Call:  
!timeout 30s claude -p --session-id "lum-$(date +%s)" "/command-lum-chan" 2>&1 || echo "FAILED: /command-lum-chan"

## Results Integration
Above commands will execute Layer 2 slash commands, which in turn call Layer 3 sub-agents.
```

#### 重要なポイント
- `--session-id` で一意のセッションを生成（セッション競合回避）
- `timeout` でハング防止
- `2>&1` でエラー出力も取得
- `|| echo "FAILED..."` でエラーハンドリング

### 手法2: ファイルベース通信方式

#### 原理
ファイルシステムを中間層として使用し、コマンド実行指示をファイル経由で伝達。

#### 実装方法
```markdown
# Layer 1: コマンドキューに追加
!echo "/command-zundamon" >> /tmp/claude_command_queue.txt
!echo "/command-lum-chan" >> /tmp/claude_command_queue.txt

# Layer 1: ポーリング実行スクリプト起動
!bash -c '
while IFS= read -r cmd; do
  echo "Executing: $cmd"
  claude -p --session-id "queue-$(date +%s)" "$cmd" 2>&1
done < /tmp/claude_command_queue.txt
rm -f /tmp/claude_command_queue.txt
'
```

### 手法3: 疑似実行方式（フォールバック）

#### 原理
カスタムスラッシュコマンドの内容を読み取り、専用サブエージェントで疑似実行。

#### 実装方法
```markdown
# Layer 1: マークダウン内容を読み取り
Layer 2 command content:
!cat .claude/commands/no-context/command-zundamon.md

# Task呼び出しで疑似実行
Based on the above content, I will execute the equivalent functionality using Task tool with zundamon-agent.
```

## 📚 技術的根拠と参考資料

### 公式ドキュメント
- **[Slash Commands - Anthropic](https://docs.anthropic.com/en/docs/claude-code/slash-commands)**
  - カスタムスラッシュコマンドの基本仕様
  - 引数処理（`$ARGUMENTS`, `$1`, `$2`など）

- **[Common Workflows - Anthropic](https://docs.anthropic.com/en/docs/claude-code/common-workflows)**
  - 一般的なワークフロー例

- **[Claude Code Best Practices - Anthropic](https://www.anthropic.com/engineering/claude-code-best-practices)**
  - `-p` フラグによるプログラム統合
  - ヘッドレスモード実行

### 重要な発見源
- **[Steve Kinney - Claude Code and Bash Scripts](https://stevekinney.com/courses/ai-development/claude-code-and-bash-scripts)**
  - ⭐ **Key Discovery**: bashコマンド埋め込み（`!`）機能の詳細説明
  - ヘッドレスモード（`claude -p`）との組み合わせ
  - マルチエージェントワークフロー

- **[GitHub - zhsama/claude-sub-agent](https://github.com/zhsama/claude-sub-agent)**
  - AI駆動開発ワークフローシステム
  - サブエージェント協調の実装例

- **[GitHub - qdhenry/Claude-Command-Suite](https://github.com/qdhenry/Claude-Command-Suite)**
  - プロフェッショナルなスラッシュコマンド集
  - `/orchestration:*` ネームスペースでのタスク管理

### コミュニティ事例
- **Reddit r/ClaudeAI**: "is claude able to use custom slash commands inside a custom slash command?"
  - 同じ課題に取り組むユーザーの議論
  - 実践的な解決方法の共有

## 🧪 テスト結果

### ✅ 成功した機能
1. **基本的なbashコマンド埋め込み**: `!date`, `!pwd`, `!ls` など
2. **Claude CLI実行**: `claude --version` の実行確認
3. **セッション分離**: `--session-id` による競合回避
4. **エラーハンドリング**: timeout, エラー出力取得

### ❓ 検証中の項目
1. **カスタムスラッシュコマンド呼び出し**: `claude -p "/command-name"` の動作
2. **出力取得**: Layer 2からの戻り値の正確な受け取り
3. **セッション安定性**: 大量実行時の安定性

### ❌ 制限事項
1. **セッション競合**: 同一セッション内での複数Claude実行は困難
2. **タイムアウト**: 長時間実行するコマンドの処理
3. **並列実行**: 複数スラッシュコマンドの同時実行制限

## 💡 実装のベストプラクティス

### 1. セッション管理
```bash
# 一意のセッションID生成
session_id="task-$(date +%s)-$$"
claude -p --session-id "$session_id" "/command-name"
```

### 2. エラーハンドリング
```bash
# タイムアウト + エラー処理
timeout 30s claude -p "/command-name" 2>&1 || {
    echo "ERROR: Command failed or timed out"
    exit 1
}
```

### 3. 出力整形
```bash
# JSON出力での構造化
claude -p --output-format json "/command-name" | jq '.'
```

### 4. 環境チェック
```bash
# 前提条件確認
!which claude > /dev/null || echo "ERROR: Claude CLI not available"
!test -f .claude/commands/no-context/target-command.md || echo "ERROR: Target command not found"
```

## 🚀 実装ガイド

### ステップ1: 基本構造の準備
```bash
mkdir -p .claude/commands/no-context
mkdir -p docs/investigation
```

### ステップ2: Master Orchestrator作成
```markdown
---
name: master-orchestrator
description: Orchestrates multiple slash commands with bash embedding
tools: [Bash, Read]
---

# Master Orchestrator

## Environment Check
Claude CLI: !which claude
Working Directory: !pwd
Available Commands: !ls -la .claude/commands/no-context/

## Layer 2 Execution
!timeout 30s claude -p --session-id "master-$(date +%s)" "/target-command" 2>&1
```

### ステップ3: テスト実行
1. `/master-orchestrator` を実行
2. 出力ログを確認
3. Layer 2, Layer 3の動作を検証

## 🔮 今後の発展

### 期待される改善
1. **公式API**: Anthropicによるスラッシュコマンド間呼び出しAPIの提供
2. **MCP統合**: Model Context Protocolとの連携強化
3. **GUI管理**: ワークフロー可視化ツール

### 応用例
1. **CI/CDパイプライン**: テスト → ビルド → デプロイの自動化
2. **コードレビュー**: 複数観点での自動レビュー実行
3. **ドキュメント生成**: 設計 → 実装 → ドキュメント化の一貫実行

## 📄 関連ファイル

- `docs/investigation/three-tier-architecture-investigation.md` - 全体調査結果
- `docs/investigation/execution-flow-comparison.md` - 実行フロー比較
- `.claude/commands/no-context/orchestrator-hack.md` - 実証テストコマンド
- `.claude/commands/no-context/test-bash-embed.md` - 基本機能テスト

---

**最終更新**: 2025-09-06  
**ステータス**: 実証テスト実施中  
**次のステップ**: 実際の3層アーキテクチャでの動作確認