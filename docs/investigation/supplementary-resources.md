# 補足資料とリソース集

## 🔗 重要な参考URL

### 公式Anthropicドキュメント
- **[Slash Commands - Anthropic](https://docs.anthropic.com/en/docs/claude-code/slash-commands)**
  - カスタムスラッシュコマンドの公式仕様
  - 引数処理、フロントマター設定、MCP統合
  - 最終更新: 2025年

- **[Common Workflows - Anthropic](https://docs.anthropic.com/en/docs/claude-code/common-workflows)**
  - Claude Code実用ワークフローのベストプラクティス
  - プロジェクト初期化からデプロイまでの包括的な例

- **[Claude Code Best Practices - Anthropic](https://www.anthropic.com/engineering/claude-code-best-practices)**
  - ⭐ **Key Resource**: ヘッドレスモード（`claude -p`）の詳細
  - プログラマティック統合のガイドライン

### 重要な技術ブログ・記事

- **[Steve Kinney - Claude Code and Bash Scripts](https://stevekinney.com/courses/ai-development/claude-code-and-bash-scripts)**
  - ⭐ **Critical Discovery**: `!` によるbashコマンド埋め込みの発見源
  - マルチエージェントワークフロー、Git worktrees活用法
  - ヘッドレスモードでの自動化テクニック

- **[Claude Code Tips & Tricks: Custom Slash Commands](https://cloudartisan.com/posts/2025-04-14-claude-code-tips-slash-commands/)**
  - Hugo Webサイト構築における実践的なカスタムコマンド例
  - ネームスペース化コマンド（`/project:posts:new`）の設計

- **[How I use Claude Code (+ my best tips)](https://www.builder.io/blog/claude-code)**
  - 実用的な使用方法とカスタムコマンド作成のコツ
  - フック機能の活用法

- **[Claude Code Slash Commands: Boost Your Productivity](https://alexop.dev/tils/claude-code-slash-commands-boost-productivity/)**
  - 自然言語でのコマンド説明とCLAUDE.mdファイル活用
  - 生産性向上のための実践的なコマンド設計

### 高品質なGitHubリポジトリ

- **[zhsama/claude-sub-agent](https://github.com/zhsama/claude-sub-agent)**
  - ⭐ **Architecture Reference**: AI駆動開発ワークフローシステム
  - スペックオーケストレーター、品質ゲート、フェーズ分割
  - 本調査の3層アーキテクチャと概念的に類似

- **[qdhenry/Claude-Command-Suite](https://github.com/qdhenry/Claude-Command-Suite)**
  - プロフェッショナルなスラッシュコマンド集
  - `/orchestration:*` ネームスペースでのタスク管理
  - タスク分解、進捗追跡、Git統合

- **[hesreallyhim/awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code)**
  - Claude Codeリソースのキュレーションリスト
  - コミュニティ発のコマンド、ワークフロー、ツール集

### コミュニティディスカッション

- **[Reddit r/ClaudeAI - カスタムスラッシュコマンド内でのスラッシュコマンド使用](https://www.reddit.com/r/ClaudeAI/comments/1lwsfo4/is_claude_able_to_use_custom_slash_commands/)**
  - 本調査と全く同じ課題についてのコミュニティ議論
  - 実際のユーザーが遭遇した問題と解決試行

- **[Reddit r/ClaudeAI - Markdownファイルからのスラッシュコマンド実行](https://www.reddit.com/r/ClaudeAI/comments/1lf4b9i/can_claude_code_execute_slash_commands_from/)**
  - Markdownファイル内でのスラッシュコマンド実行可能性の議論

### 技術ガイド・チュートリアル

- **[Claude Code Commands & Workflows - Complete Guide](https://claudecode.io/commands)**
  - 60+のスラッシュコマンド完全ガイド
  - Git、テスト、デプロイ自動化のワークフロー

- **[Claude Code Hooks: Automate Your Development Workflow](https://liquidmetal.ai/casesAndBlogs/claude-code-hooks-guide/)**
  - フック機能による開発ワークフロー自動化
  - シェルコマンド実行、カスタム権限管理

- **[20 Claude Code CLI Commands to Make Your 10x Productive](https://apidog.com/blog/claude-code-cli-commands/)**
  - CLI生産性向上のための実践的コマンド集

- **[The Claude Code Complete Guide: Learn Vibe-Coding & Agentic AI](https://natesnewsletter.substack.com/p/the-claude-code-complete-guide-learn)**
  - Anthropicのターミナルベースド AI活用法
  - マルチエージェントワークフロー、コスト制御

### 探索された技術記事

- **[Poking Around Claude Code](https://leehanchung.github.io/blogs/2025/03/07/claude-code/)**
  - Claude Codeの内部仕組み探索
  - LLMエージェント、MCP、制御フロー分析

## 📋 技術仕様まとめ

### bashコマンド埋め込み仕様
**構文**: `!command`
**実行タイミング**: マークダウン処理時
**出力**: プロンプトに直接挿入
**制限**: シェル環境に依存、長時間実行は要注意

### Claude CLI仕様
**基本形**: `claude [options] [command] [prompt]`
**ヘッドレスモード**: `-p, --print`
**出力フォーマット**: `--output-format json|stream-json`
**セッション指定**: `--session-id <uuid>`

### カスタムスラッシュコマンド仕様
**ファイル場所**: 
- プロジェクト: `.claude/commands/`
- 個人: `~/.claude/commands/`
**フォーマット**: Markdown with YAML frontmatter
**引数アクセス**: `$ARGUMENTS`, `$1`, `$2`, etc.

## 🔬 実験データ

### 成功したコマンド例
```bash
# 基本情報取得
!date                    # ✅ 成功
!pwd                     # ✅ 成功  
!which claude            # ✅ 成功

# ファイル操作
!ls -la .claude/         # ✅ 成功
!cat file.md            # ✅ 成功

# プロセス情報
!ps aux | grep claude   # ✅ 成功
```

### 検証中のコマンド例
```bash
# スラッシュコマンド呼び出し
!claude -p "/help"                    # ❓ 検証中
!claude -p --session-id "test" "/cmd" # ❓ 検証中

# 並列実行
!claude -p "/cmd1" & claude -p "/cmd2" # ❓ 検証中
```

## 📊 パフォーマンス考慮事項

### メモリ使用量
- **問題**: JavaScript heap out of memory
- **対策**: プロセス分離、実行数制限
- **監視**: `ps aux`, `top` での使用量確認

### 実行時間
- **タイムアウト設定**: `timeout 30s`
- **推奨値**: 短いタスク: 10-30秒、長いタスク: 1-5分
- **エラー処理**: タイムアウト時の適切な後処理

### セッション管理
- **競合回避**: 一意のセッションID生成
- **推奨パターン**: `"task-$(date +%s)-$$"`
- **クリーンアップ**: セッション終了後の適切な処理

## 🛠 デバッグ・トラブルシューティング

### よくある問題

1. **セッション競合**
   - 現象: コマンドがハングまたは失敗
   - 解決: `--session-id` で一意ID指定

2. **長時間実行**
   - 現象: コマンドが応答しない
   - 解決: `timeout` コマンドで制限

3. **出力が取得できない**
   - 現象: bashコマンドの出力が表示されない
   - 解決: `2>&1` でエラー出力も取得

### デバッグ用コマンド
```bash
# プロセス確認
!ps aux | grep -i claude | wc -l

# ファイル存在確認
!test -f .claude/commands/target.md && echo "OK" || echo "NOT FOUND"

# 権限確認
!ls -la $(which claude)

# 環境変数確認
!env | grep -i claude
```

## 📚 学習リソース

### 初心者向け
1. 公式ドキュメントのSlash Commandsセクション
2. Steve KinneyのBash Scripts記事
3. awesome-claude-codeリポジトリ

### 中級者向け
1. Claude-Command-Suiteの実装研究
2. claude-sub-agentのアーキテクチャ分析
3. コミュニティディスカッションの追跡

### 上級者向け
1. Model Context Protocol (MCP) 深掘り
2. マルチエージェントシステム設計
3. 大規模ワークフロー自動化

---

**このリソース集は継続的に更新されます。新しい発見や有用なリンクがあれば随時追加してください。**