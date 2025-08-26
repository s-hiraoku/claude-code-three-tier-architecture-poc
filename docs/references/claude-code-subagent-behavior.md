# Claude Code サブエージェント動作仕様調査報告

## 調査日時
2025-08-26

## 調査目的
Claude CodeのTaskツールを使用したサブエージェント間でのコンテキスト受け渡し動作を明確化するため。

## 調査結果

### サブエージェントの基本動作

#### 独立したコンテキストウィンドウ
```
"Each subagent operates in its own context, preventing pollution 
of the main conversation and keeping it focused on high-level objectives."
```
**出典**: [Anthropic Claude Code Subagents Documentation](https://docs.anthropic.com/en/docs/claude-code/sub-agents)

### 重要な発見

#### 1. メモリ空間の独立性
- **各サブエージェントは独立したコンテキストを持つ**
- 呼び出し元エージェントの変数やメモリに**自動的にはアクセスできない**
- メインエージェントのローカル変数（`<let>`で定義された変数など）はサブエージェントに継承されない

#### 2. 情報受け渡し方法の制限
- **Taskツールのpromptパラメータのみ**が唯一のデータ受け渡し手段
- 構造化されたデータを渡すには**JSON文字列化**が必要
- その他のパラメータ（例：context オブジェクト直接受け渡し）は不可

#### 3. サブエージェント間の協調パターン
正しいパターン：
```
Layer 2 Agent (context管理)
    ↓ Task(prompt=JSON.stringify(context + instruction))
Layer 3 Sub-Agent A → 結果を Layer 2 に返却
    ↑
Layer 2 Agent (context更新: A の結果を追加)
    ↓ Task(prompt=JSON.stringify(updated_context + instruction))
Layer 3 Sub-Agent B (A の結果を参照可能)
```

間違ったパターン：
```
❌ Sub-Agent A → Sub-Agent B（直接通信は不可）
❌ Sub-Agent A ← shared_memory → Sub-Agent B（共有メモリなし）
```

#### 4. コンテキストハブパターン
- **Layer 2 エージェントが「コンテキストハブ」として機能**
- Layer 2 が全サブエージェントの結果を収集・管理
- 累積結果を次のサブエージェントに渡す責任を持つ

### 実装への影響

#### 必要な設計要素
1. **明示的なコンテキスト受け渡し**: JSONシリアライズが必須
2. **Layer 2 での状態管理**: accumulated_results の更新と管理
3. **プロンプト設計**: コンテキスト情報を含む詳細なプロンプト作成

#### POMLでの実装パターン
```xml
<!-- Layer 2 Agent -->
<poml>
  <let name="context">{{ initial_context }}</let>
  
  <task>
    1. サブエージェント A を context と共に呼び出し
    2. 結果を context.accumulated_results に追加
    3. サブエージェント B を更新された context と共に呼び出し
  </task>
</poml>
```

#### Taskツール呼び出し例
```javascript
// Layer 2 から Layer 3 への呼び出し
Task({
  subagent_type: "zundamon-greeter",
  prompt: `
    コンテキスト: ${JSON.stringify(context)}
    
    あなたの役割: ずんだもんとして挨拶
    お題: ${context.topic}
    前の回答: ${JSON.stringify(context.accumulated_results)}
    
    上記を考慮して回答してください。
  `
});
```

### 検証された設計原則

#### ✅ 正しい理解
- サブエージェント間の直接通信は不可
- Layer 2 エージェントがコンテキスト管理の責任を持つ
- プロンプトによる明示的なデータ受け渡しが必須

#### ❌ 間違った仮定
- サブエージェントが自動的に親エージェントの変数を継承
- サブエージェント間での暗黙的な状態共有
- コンテキストオブジェクトの自動受け渡し

### 今後の実装方針

1. **Layer 2 エージェントの強化**: コンテキスト管理機能の実装
2. **プロンプト設計の標準化**: JSONコンテキストを含むプロンプトテンプレート
3. **累積結果管理**: structured な結果収集・更新メカニズム
4. **エラーハンドリング**: コンテキスト破損時の回復処理

### 参考資料
- [Claude Code Overview - Anthropic](https://docs.anthropic.com/en/docs/claude-code/overview)
- [Subagents - Anthropic](https://docs.anthropic.com/en/docs/claude-code/sub-agents)
- [Claude Code Best Practices - Anthropic](https://www.anthropic.com/engineering/claude-code-best-practices)

---

この調査により、3層アーキテクチャにおけるコンテキスト受け渡し設計の技術的根拠が明確化されました。