# with-context 実装設計仕様

## 作成日時
2025-08-26

## 概要
Claude Code 3層アーキテクチャにおける協調的サブエージェント動作を検証するための `with-context` 実装の設計仕様。協調回答形式と議論形式の両方のパターンを実装し、POMLによるコンテキスト変数管理の柔軟性を検証する。

## 🎯 実装目的

### 検証したいこと
1. **協調的サブエージェント動作**: 各サブエージェントが前の回答/発言を考慮した応答を生成
2. **POMLによるコンテキスト変数管理**: `<let>`変数を使った動的な情報伝達
3. **Layer 2のコンテキストハブ機能**: POML変数でのサブエージェント間情報仲介
4. **複数の協調パターン**: 協調回答形式と議論形式の両方の実装

### 協調パターンの種類

#### Pattern A: 協調回答形式
- **挨拶**: 初期の友好的な応答
- **物語/感情**: 前の回答を考慮した発展的な内容
- **統合**: 全体として調和のとれた協調的応答

#### Pattern B: 議論形式  
- **意見表明**: 初期の立場・意見の提示
- **反論・別視点**: 前の意見への反論や別の観点の提示
- **再反論**: 反論への応答・自説の補強
- **最終見解**: 全体の議論を踏まえた結論

### no-contextとの差分
- **no-context**: 独立したサブエージェント実行
- **with-context**: 協調的サブエージェント実行（協調回答または議論形式で前の結果を考慮）

## 🏗️ 構成設計

### ファイル構成（発展型汎用サブエージェント）

#### Layer 1 & Layer 2（共通）
```
.claude/commands/with-context/
├── orchestrator.md               # Layer 1: オーケストレーター
├── command-zundamon.md          # Layer 2: ずんだもんコマンド
└── command-lum-chan.md          # Layer 2: ラムちゃんコマンド

poml/commands/with-context/
├── orchestrator.poml            # Layer 1 動作制御
├── zundamon.poml               # Layer 2 動作制御
└── lum.poml                    # Layer 2 動作制御
```

#### Layer 3（発展型汎用サブエージェント）
```
.claude/agents/with-context/
├── zundamon.md                  # 汎用ずんだもんエージェント
└── lum.md                       # 汎用ラムちゃんエージェント

poml/agents/with-context/
├── zundamon-behavior.poml       # ずんだもん汎用動作定義
└── lum-behavior.poml           # ラムちゃん汎用動作定義
```

#### コンテキストによる動作制御方式
各サブエージェントは、POMLから渡されるコンテキスト情報に基づいて動作を決定：

**協調回答モード**:
```json
{
  "topic": "今日の天気について語って",
  "mode": "collaborative_response", 
  "my_role": "greeter|storyteller|emotion_expresser",
  "previous_responses": [...],
  "interaction_style": "friendly_cooperative"
}
```

**議論モード**:
```json
{
  "topic": "今日の天気について議論して",
  "mode": "debate",
  "my_role": "opinion_maker|counter_arguer|rebuttal_maker|conclusion_maker", 
  "previous_statements": [...],
  "interaction_style": "logical_argumentative"
}
```

## 🔄 コンテキスト伝播フロー（POML変数ベース）

### Layer 1: Orchestrator
**責務**: お題設定・Layer 2への委譲
```xml
<!-- orchestrator.poml -->
<let name="topic">${ARGUMENTS}</let>
<let name="all_results">[]</let>

処理フロー:
  1. $ARGUMENTSからお題を取得してtopic変数に設定
  2. command-zundamon.mdを読み込み、topicを渡して実行
  3. zundamonの結果をall_resultsに追加
  4. command-lum-chan.mdを読み込み、topic + all_resultsを渡して実行  
  5. 統合結果を表示
```

**$ARGUMENTS対応**:
- 実行時にお題を動的に指定可能
- 例: `/orchestrator "今日の天気について語って"`
- 例: `/orchestrator "好きな食べ物について話して"`
- 例: `/orchestrator "夏の思い出を教えて"`

**知らないこと**: Layer 3のサブエージェント名・実行詳細

### Layer 2: Commands（コンテキストハブ）
**責務**: サブエージェント選択・協調管理

#### command-zundamon (Layer 2) - 汎用エージェント制御
```xml
<!-- zundamon.poml -->
<let name="topic">{{ inherited_topic }}</let>
<let name="mode">{{ inherited_mode }}</let>  <!-- "collaborative_response" or "debate" -->
<let name="previous_context">{{ inherited_previous_context }}</let>
<let name="my_results">[]</let>

汎用エージェント制御フロー:
  1. モードに応じて適切なロールでzundamonエージェントを呼び出し
     - collaborative_response: "greeter" ロールで呼び出し
     - debate: "opinion_maker" ロールで呼び出し
  2. 結果をmy_resultsに追加
  3. モードに応じて第2ラウンドでzundamonエージェントを呼び出し
     - collaborative_response: "storyteller" ロールで呼び出し
     - debate: "rebuttal_maker" ロールで呼び出し
  4. 結果をmy_resultsに追加
  5. my_resultsをLayer 1に返却
```

#### command-lum-chan (Layer 2) - 汎用エージェント制御
```xml
<!-- lum.poml -->
<let name="topic">{{ inherited_topic }}</let>
<let name="mode">{{ inherited_mode }}</let>
<let name="previous_context">{{ inherited_previous_context }}</let>
<let name="my_results">[]</let>

汎用エージェント制御フロー:
  1. モードに応じて適切なロールでlumエージェントを呼び出し
     - collaborative_response: "greeter" ロールで呼び出し
     - debate: "counter_arguer" ロールで呼び出し
  2. 結果をmy_resultsに追加
  3. モードに応じて第2ラウンドでlumエージェントを呼び出し
     - collaborative_response: "emotion_expresser" ロールで呼び出し
     - debate: "conclusion_maker" ロールで呼び出し
  4. 結果をmy_resultsに追加
  5. my_resultsをLayer 1に返却
```

### Layer 3: Sub-Agents（汎用キャラクターエージェント）
**責務**: コンテキストに応じた柔軟な応答生成

#### 汎用エージェントの動作原理
**zundamon.md** と **lum.md** は、コンテキスト情報に基づいて動作を動的に変更：

#### コンテキストベースの役割切り替え
```
協調回答モード時:
- greeter: 親しみやすい挨拶・反応
- storyteller: 物語やエピソードの作成
- emotion_expresser: 感情表現や共感の示し

議論モード時:
- opinion_maker: 明確な立場・意見の表明
- counter_arguer: 反論・別視点の提示
- rebuttal_maker: 反論への応答・自説の補強
- conclusion_maker: 全体議論を踏まえた最終見解
```

#### エージェントの動作原則
各サブエージェントは：
- Layer 2から渡される構造化コンテキスト（mode, role, topic, previous_context）を解析
- キャラクターの本質を保ちつつ、指定されたロールに応じた応答を生成
- 前のコンテキストとの一貫性を保ちつつ、適切な協調性または論理性を発揮

## 📋 責務分離の詳細

### Layer 1 (Orchestrator)
**やること**:
- ✅ POMLファイル読み込み（$ARGUMENTSからお題取得）
- ✅ Layer 2のマークダウンファイル読み込み・実行
- ✅ 実行結果の統合表示

**やらないこと**:
- ❌ サブエージェントの選択・管理
- ❌ 協調動作の詳細制御  
- ❌ Layer 3の存在を意識

### Layer 2 (Commands)
**やること**:
- ✅ Layer 1からの「お題」受け取り
- ✅ 適切なサブエージェントの選択
- ✅ サブエージェント間の実行順序制御
- ✅ 前の結果を次のサブエージェントに伝達（コンテキストハブ）
- ✅ POML変数による動的なコンテキスト管理

**やらないこと**:
- ❌ 構造化JSONコンテキストの管理
- ❌ Layer 1の実行順序への関与

### Layer 3 (Sub-Agents)
**やること**:
- ✅ Layer 2からの指示に基づく具体的処理
- ✅ 前の結果を考慮した協調的応答
- ✅ 専門分野での高品質な出力

**やらないこと**:
- ❌ 他のサブエージェントとの直接通信
- ❌ 全体フローの管理

## 🔍 検証ポイント

### 汎用エージェントの成功基準

#### 協調回答モードの成功基準
1. **zundamon (greeter role)**: お題に対する親しみやすい挨拶・初期反応
2. **zundamon (storyteller role)**: greeterの内容を踏まえた物語・エピソードの展開
3. **lum (greeter role)**: 前の2つの内容を考慮した友好的な挨拶・共感
4. **lum (emotion_expresser role)**: 全体を通した感情表現・結束

#### 議論モードの成功基準
1. **zundamon (opinion_maker role)**: お題に対する明確な立場・意見の表明
2. **lum (counter_arguer role)**: ずんだもんの意見を踏まえた反論・別視点の提示
3. **zundamon (rebuttal_maker role)**: ラムちゃんの反論に対する論理的な応答・反駁
4. **lum (conclusion_maker role)**: 全体の議論を総括した最終見解・結論

### 技術検証項目
- [ ] POMLの`<let>`変数による汎用コンテキストの情報伝達
- [ ] Layer 2でのPOML変数ベース汎用エージェントハブ機能
- [ ] サブエージェントのコンテキストベース動作切り替え
- [ ] 同一エージェントでの協調回答と議論の両方の実現
- [ ] キャラクター一貫性を保ったロール切り替え
- [ ] 構造化JSONコンテキスト不使用での汎用エージェント実現

## 🛠️ 実装計画

### Phase 1: 基本ファイル作成
1. **Layer 1**: orchestrator.md, orchestrator.poml
2. **Layer 2**: command-*.md, *.poml  
3. **Layer 3**: サブエージェント.md, *-behavior.poml

### Phase 2: 協調動作の実装
1. **コンテキスト伝達機能**: Layer 2での前結果管理
2. **プロンプト設計**: 協調を促すプロンプト作成
3. **統合出力**: 各層の結果統合

### Phase 3: 検証・調整
1. **動作確認**: 期待される協調動作の実現
2. **デバッグ**: 問題点の特定・修正
3. **ドキュメント更新**: 実装結果の記録

## 📚 参考実装

### no-context実装との比較参照
- 基本的な3層アーキテクチャ構造は同一
- 差分は Layer 2 でのコンテキスト管理のみ
- Layer 1, Layer 3 の責務は基本的に同じ

## 🎯 期待される結果

### 実行例

#### 実行コマンド
```bash
/orchestrator "今日の天気について語って"
```

#### 期待される議論出力
```
お題: 「今日の天気について語って」

1. zundamon-opinion: "今日は晴れで最高なのだ！晴れの日は外でずんだ餅を作るのが
   一番おいしいのだ。みんな外に出て自然を楽しむべきなのだ〜"

2. lum-counter: "ちょっと待つっちゃ！晴れもいいけど、うちは雨の日も好きだっちゃ。
   雨の音を聞きながら家でゆっくりするのも素敵だっちゃ〜。ずんだもんは
   外ばかり考えてるっちゃ！" (ずんだもんの意見への反論)

3. zundamon-rebuttal: "ラムちゃんの言うことも分かるのだ。でも晴れの日の
   開放感は特別なのだ！雨の日も確かにいいけど、みんなで外で遊べる
   晴れの日の方が楽しいのだ〜" (反論への応答・自説の補強)

4. lum-conclusion: "うーん、ずんだもんとお話しして分かったっちゃ。
   晴れも雨もそれぞれ良さがあるっちゃね。大事なのは、どんな天気でも
   楽しい気持ちでいることだっちゃ〜♪" (全議論を踏まえた最終見解)
```

#### 他の議論テーマ例
```bash
/orchestrator "好きな食べ物について議論して"
/orchestrator "夏と冬どちらが良いか議論して"
/orchestrator "早起きと夜更かしについて議論して"
/orchestrator "読書と映画鑑賞どちらが良いか議論して"
```

## 🔬 論文ベース機能拡張（2次開発予定）

### 追加機能一覧

**注意**: 以下の機能は2次開発フェーズで実装予定。まずは基本的なwith-context実装に集中する。

論文調査により特定された、将来実装予定の追加機能とPOML標準機能での実現方法：

#### 1. Response Consistency Index (RCI) - 応答一貫性評価
**論文根拠**: "Modeling Response Consistency in Multi-Agent LLM Systems" (2504.07303v1)
**目的**: 協調回答と議論の品質を定量評価

**POML実装**:
```xml
<!-- RCI計算 - キャラクター一貫性チェック -->
<let name="rci_score">0</let>
<let name="total_checks">0</let>

<!-- ずんだもん応答の一貫性チェック -->
<if condition="{{ zundamon_response.includes('なのだ') }}">
  <let name="rci_score">{{ rci_score + 1 }}</let>
</if>
<let name="total_checks">{{ total_checks + 1 }}</let>

<!-- ラムちゃん応答の一貫性チェック -->
<if condition="{{ lum_response.includes('だっちゃ') }}">
  <let name="rci_score">{{ rci_score + 1 }}</let>
</if>
<let name="total_checks">{{ total_checks + 1 }}</let>

<!-- コンテキスト忠実性チェック -->
<if condition="{{ response.toLowerCase().includes(topic.toLowerCase()) }}">
  <let name="rci_score">{{ rci_score + 1 }}</let>
</if>
<let name="total_checks">{{ total_checks + 1 }}</let>

<!-- 最終RCIスコア計算 -->
<let name="final_rci">{{ (rci_score / total_checks * 100).toFixed(1) }}</let>
```

#### 2. コンテキスト忠実性検証 (Context Faithfulness)
**論文根拠**: "Context-faithful Prompting for Large Language Models" (2303.11315)
**目的**: キャラクター特性の確実な反映

**POML実装**:
```xml
<!-- コンテキスト忠実性チェック -->
<let name="context_warnings">[]</let>

<!-- キャラクター特性チェック -->
<if condition="{{ !zundamon_response.includes('なのだ') && character === 'zundamon' }}">
  <let name="context_warnings">[...context_warnings, "ずんだもん口調が不足"]</let>
</if>

<!-- トピック関連性チェック -->
<if condition="{{ !response.toLowerCase().includes(topic.toLowerCase()) }}">
  <let name="context_warnings">[...context_warnings, "トピックとの関連性が低い"]</let>
</if>

<!-- ロール整合性チェック -->
<if condition="{{ role === 'opinion_maker' && !response.includes('思う') }}">
  <let name="context_warnings">[...context_warnings, "意見表明ロールが不十分"]</let>
</if>
```

#### 3. 動的プロンプト最適化 (Dynamic Prompt Selection)
**論文根拠**: "Selective Prompting Tuning for Personalized Conversations" (2406.18187v1)
**目的**: コンテキスト駆動型ロール切り替えの精度向上

**POML実装**:
```xml
<!-- モードとロールに基づく動的プロンプト選択 -->
<let name="selected_prompt">""</let>

<if condition="{{ mode === 'collaborative_response' && role === 'greeter' }}">
  <let name="selected_prompt">親しみやすく挨拶をして、{{ topic }}について前向きなコメントをしてください。</let>
</if>

<if condition="{{ mode === 'debate' && role === 'opinion_maker' }}">
  <let name="selected_prompt">{{ topic }}について明確な立場を取り、論理的な根拠とともに意見を述べてください。</let>
</if>

<if condition="{{ mode === 'debate' && role === 'counter_arguer' }}">
  <let name="selected_prompt">前の意見「{{ previous_statement }}」に対して、異なる視点から建設的な反論を行ってください。</let>
</if>
```

#### 4. バッチ処理最適化 (Batch Processing Optimization)
**論文根拠**: "Collaborative Stance Detection via Small-Large Language Model Consistency Verification" (2502.19954v2)
**目的**: 実用性向上とレスポンス時間短縮

**POML実装**:
```xml
<!-- 効率的な順次実行とコンテキスト蓄積 -->
<let name="batch_context">{
  "topic": "{{ topic }}",
  "mode": "{{ mode }}",
  "accumulated_responses": [],
  "execution_order": ["zundamon_1", "lum_1", "zundamon_2", "lum_2"]
}</let>

<!-- バッチ実行ループ -->
<for each="agent_turn" in="{{ batch_context.execution_order }}">
  <let name="current_context">{{ {...batch_context, current_turn: agent_turn} }}</let>
  <!-- エージェント実行とコンテキスト更新 -->
  <let name="batch_context.accumulated_responses">[...batch_context.accumulated_responses, agent_response]</let>
</for>
```

#### 5. 論理一貫性検証 (Logical Consistency Verification)
**論文根拠**: CoVerフレームワーク (2502.19954v2)
**目的**: 応答の論理的整合性確保

**POML実装**:
```xml
<!-- 論理一貫性チェック -->
<let name="consistency_checks">[]</let>

<!-- 前後の発言の矛盾チェック -->
<if condition="{{ previous_position === 'positive' && current_position === 'negative' && !explanation_given }}">
  <let name="consistency_checks">[...consistency_checks, "立場変更の説明が不足"]</let>
</if>

<!-- 議論モードでの論理構造チェック -->
<if condition="{{ mode === 'debate' && !response.includes('なぜなら') && !response.includes('理由は') }}">
  <let name="consistency_checks">[...consistency_checks, "論理的根拠が不明確"]</let>
</if>
```

### 実装統合方針

1. **RCI評価**: 各応答生成後に自動実行し、品質スコアを表示
2. **コンテキスト忠実性**: エージェント呼び出し前にプロンプト検証
3. **動的プロンプト**: Layer 2でのエージェント呼び出し時に最適プロンプト選択
4. **バッチ処理**: orchestrator.pomlでの効率的な実行順序管理
5. **一貫性検証**: 各応答後にリアルタイムで整合性確認

### 期待効果

- **定量評価**: RCIによる協調品質の客観的測定
- **品質向上**: コンテキスト忠実性による確実なキャラクター表現
- **効率性**: バッチ処理による実行時間短縮
- **信頼性**: 論理一貫性検証による応答品質保証

---

この設計により、最新の論文研究に基づく科学的根拠を持った協調的サブエージェント動作の実現を目指します。POML標準機能のみで実装可能な拡張機能により、実用性と信頼性を両立した検証システムを構築します。