# Anthropic Claude コンテキストアーキテクチャ調査報告

## 調査日時
2025-08-26

## 調査目的
Anthropic Claude のコンテキストウィンドウ構造とメモリ管理システムを理解し、我々のPOML変数ベースコンテキスト設計との比較検討を行う。

## 🏗️ Anthropic コンテキストウィンドウの基本構造

### コンテキストウィンドウの定義
> "The context window refers to the amount of text a language model can look back on and reference when generating new text."

- **機能**: 言語モデルの「作業用メモリ」として機能
- **性質**: 訓練データとは異なる、実行時の参照可能領域
- **蓄積方式**: 会話ターンごとに線形的に累積

### コンテキストサイズと制限
| モデル | 標準コンテキスト | 拡張コンテキスト | 備考 |
|-------|----------------|-----------------|------|
| Claude 3.5 | 200K tokens | - | 約500ページ相当 |
| Claude Sonnet 4 | 200K tokens | 500K tokens (Enterprise) | - |
| Claude Sonnet 4 API | - | 1M tokens | 5倍の拡張 |

## 📊 トークン管理メカニズム

### トークンの累積構造
```
総コンテキスト = 入力トークン + 出力トークン

入力フェーズ:
├── システム指示
├── 過去の会話履歴
├── プロンプト・指示
└── 現在のユーザーメッセージ

出力フェーズ:
└── モデル生成応答（次ターンの入力の一部となる）
```

### 制限到達時の動作
**新しい動作（Claude Sonnet 3.7+）**:
- 制限を超える場合は**検証エラー**を返却
- 無音でのトランケーションは行わない
- より予測可能な動作を提供

**従来の動作**:
- 古いターンを自動的に削除
- 最近の入力を優先
- 長い会話では初期の指示を忘却する可能性

## 🧠 特殊メモリ最適化機能

### Extended Thinking
```
効果的コンテキスト計算:
context_window = (input_tokens - previous_thinking_tokens) + current_turn_tokens

特徴:
- 思考ブロックは自動的にコンテキスト計算から除外
- 思考トークンは出力トークンとして1回のみ課金
- 前の思考は会話履歴から自動削除
- 実際の会話コンテンツのためのトークン容量を保持
```

### メモリ要約機能
- **Progressive Summarization**: 長い問題では完全テキストではなくコンパクトな要約を転送
- **Anchor with Labels**: 固有タグ（例：[CLIENT:XY3]）で以前の情報を参照
- **Split and Stage**: 大きな文書や計画を複数のプロンプトに分割

## 🗃️ Claude Code 階層メモリシステム

### 4階層メモリ構造
```
優先度（高→低）:
1. Enterprise Policy     # システム全体
2. Project Memory        # プロジェクト共通
3. User Memory          # ユーザー固有  
4. Project Memory Local # ローカル（非推奨）
```

### CLAUDE.mdファイル読み込み機構
```bash
# 再帰的読み込み例
/project/subdir/ で Claude Code 実行時:

読み込み順序:
1. /project/subdir/CLAUDE.md
2. /project/CLAUDE.md  
3. /CLAUDE.md
4. ルートディレクトリ / は除外

特徴:
- 上位階層が基盤を提供
- 下位階層がより具体的な記述で補強
- 自動的にコンテキストに読み込み
```

### メモリファイル構造例
```markdown
# Project Coding Standards
- Use 2-space indentation
- Follow TypeScript best practices
- Prefer functional programming patterns

# Common Commands  
- Build: npm run build
- Test: npm test
- Deploy: npm run deploy

# Team Preferences
- Code review required for all PRs
- Use conventional commit messages

# Import Additional Memories
@path/to/shared/standards.md
@team/common-workflows.md
```

### メモリ管理機能
- **Quick Add**: `#` ショートカットで素早くメモリ追加
- **Direct Edit**: `/memory` コマンドで直接編集
- **Import System**: `@path/to/file` でメモリファイルのインポート
- **Session Persistence**: セッション間でのメモリ保持

## 🔄 ベストプラクティス

### 長いコンテキストでの最適化戦略

#### 1. 文書配置の最適化
```
推奨構造:
┌─────────────────────────┐
│ 長い文書・入力（~20K+） │  ← プロンプト上部
├─────────────────────────┤
│ クエリ・指示            │
├─────────────────────────┤  
│ 例・テンプレート        │
└─────────────────────────┘
```

#### 2. 段階的要約
- **Compact Summaries**: ステップ間でフルテキストではなくコンパクトな要約を転送
- **Anchor Tags**: ユニークタグで前の情報を参照（例：[CLIENT:XY3]）
- **Manual Tracking**: 共有アウトラインで構造を手動追跡

#### 3. 分割・段階実行
- 大きな文書や計画を複数のプロンプトに分割
- 段階的実行で管理可能なチャンクに分解

## 🆚 我々のPOMLコンテキストとの比較

### 類似点

#### 階層構造
```
Anthropic Claude Code:
Enterprise → Project → User → Local

我々のPOML設計:
Layer 1 → Layer 2 → Layer 3
```

#### 情報継承メカニズム
```  
Anthropic: 上位メモリ → 下位メモリに基盤提供
我々: 上位POML変数 → 下位POML変数に継承
```

#### 永続性と管理
```
Anthropic: CLAUDE.mdファイルでセッション間保持
我々: POML変数で実行時保持（設計による）
```

### 相違点

#### ストレージ方式
| 項目 | Anthropic | 我々のPOML |
|------|-----------|------------|
| 保存場所 | ファイルシステム | 実行時メモリ |
| 永続性 | セッション間持続 | 実行時のみ |
| 管理方式 | テキストファイル編集 | POML変数操作 |

#### スコープと制御
| 項目 | Anthropic | 我々のPOML |
|------|-----------|------------|
| 適用範囲 | グローバル〜ローカル | Layer間 |
| 更新頻度 | 手動編集 | 動的更新 |
| 構造化 | マークダウン | POML変数 |

## 📋 設計への示唆

### 我々のアプローチの妥当性
1. **階層設計**: Anthropicと同様の階層的情報伝達
2. **責務分離**: 明確な層間分離（Anthropicのメモリ階層と類似）
3. **コンテキスト管理**: 実行時の動的管理（Anthropicの実行時コンテキストと類似）

### 改善の可能性
1. **永続化オプション**: 必要に応じてPOML変数の永続化検討
2. **メモリ最適化**: 長いコンテキストでの要約機能
3. **階層拡張**: より複雑な階層構造への対応

## 🎯 結論

Anthropicのコンテキストアーキテクチャ調査により、以下が確認された：

### 設計の整合性
- 我々のPOML変数ベースアプローチは、Anthropicの階層メモリシステムと**概念的に整合**
- 層間の責務分離とコンテキスト継承パターンが**Anthropicのベストプラクティスと一致**

### 技術的妥当性
- 実行時メモリ管理によるコンテキスト伝達は、Anthropicの動的コンテキスト処理と**原理的に類似**
- POML変数による情報構造化は、Claude Codeのメモリファイル構造化と**アプローチが近似**

### 実装の信頼性
- 3層アーキテクチャでのコンテキスト設計が、Anthropicの実装パターンと**技術的に矛盾しない**ことを確認

## 📚 参考資料

- [Context windows - Anthropic](https://docs.anthropic.com/en/docs/build-with-claude/context-windows)
- [Manage Claude's memory - Anthropic](https://docs.anthropic.com/en/docs/claude-code/memory)  
- [Long context prompting tips - Anthropic](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/long-context-tips)
- [How large is Claude's context window? | Anthropic Help Center](https://support.anthropic.com/en/articles/7996848-how-large-is-claude-s-context-window)

---

この調査により、我々のPOML変数ベースコンテキスト設計の技術的根拠と妥当性が、Anthropicの公式アーキテクチャとの比較によって確立された。