# 実装例とコードサンプル集

## 🎯 完全な3層アーキテクチャ実装例

### Layer 1: Master Orchestrator

**ファイル**: `.claude/commands/no-context/master-orchestrator.md`

```markdown
---
name: master-orchestrator
description: Ultimate 3-tier architecture orchestrator using bash command embedding
tools: [Bash, Read]
---

# Master Orchestrator - Layer 1

I am the ultimate orchestrator implementing true 3-tier architecture through bash command embedding.

## 🔍 Environment Verification
**Current Time**: !date
**Working Directory**: !pwd
**Claude CLI Available**: !which claude || echo "❌ Claude CLI not found"
**Available Commands**: !ls -la .claude/commands/no-context/ | grep -E '\\.md$'

## 🚀 Layer 2 Command Execution

### Zundamon Character Command
Executing Zundamon workflow with timeout protection:
!timeout 45s claude -p --session-id "zundamon-$(date +%s)" "/command-zundamon" 2>&1 || echo "⚠️ TIMEOUT: /command-zundamon execution failed"

---

### lum-chan Character Command  
Executing lum-chan workflow with timeout protection:
!timeout 45s claude -p --session-id "lum-$(date +%s)" "/command-lum-chan" 2>&1 || echo "⚠️ TIMEOUT: /command-lum-chan execution failed"

---

## 📊 Execution Summary

If both Layer 2 commands executed successfully above, we have achieved:

- ✅ **Layer 1**: This master orchestrator (slash command)
- ✅ **Layer 2**: Character commands (slash commands called via bash)  
- ✅ **Layer 3**: Sub-agents (called by Layer 2 via Task tool)

## 🔧 Debug Information
**Process Count**: !ps aux | grep -i claude | grep -v grep | wc -l
**Memory Usage**: !ps aux | grep -i claude | grep -v grep | awk '{sum+=$6} END {print "Total Memory: " sum " KB"}'

This is the **ULTIMATE 3-TIER ARCHITECTURE** implementation!
```

### Layer 2: Enhanced Character Commands

**ファイル**: `.claude/commands/no-context/command-zundamon.md`

```markdown
---
name: command-zundamon
description: Enhanced Zundamon character command with POML integration and sub-agent orchestration
tools: [Read, Bash, Task]
---

# Zundamon Command - Layer 2

I am the enhanced Zundamon character command with full Layer 3 orchestration capabilities.

## 🔍 System Status Check
**Execution Time**: !date
**Working Directory**: !pwd
**POML Available**: !which pomljs || echo "Installing pomljs..." && npm install -g pomljs

## 📜 POML Behavior Analysis
Reading Zundamon behavior specification:
!npx pomljs --file poml/commands/no-context/zundamon.poml || echo "⚠️ Failed to read POML file"

## 🤖 Layer 3 Sub-Agent Orchestration

Based on the POML specification, I will now orchestrate the following Layer 3 sub-agents:

### Sub-Agent 1: Zundamon Greeter
I will call the zundamon-greeter sub-agent for greeting functionality.

### Sub-Agent 2: Zundamon Storyteller  
I will call the zundamon-storyteller sub-agent for storytelling functionality.

### Integration Process
The sub-agents will be called using the Task tool, and their results will be integrated into a cohesive Zundamon character response.

## 🎭 Character Response Integration
All Layer 3 sub-agent responses will be combined with Zundamon's characteristic speech patterns:
- Uses "〜なのだ" and "〜のだ" endings
- References zunda mochi and Tohoku region
- Maintains energetic and friendly personality

## 📊 Execution Metrics  
**Sub-agents Called**: 2 (zundamon-greeter, zundamon-storyteller)
**Expected Response Time**: 30-60 seconds
**Success Criteria**: Both sub-agents respond with character-appropriate content
```

**ファイル**: `.claude/commands/no-context/command-lum-chan.md`

```markdown
---
name: command-lum-chan
description: Enhanced lum-chan character command with POML integration and sub-agent orchestration
tools: [Read, Bash, Task]
---

# lum-chan Command - Layer 2

I am the enhanced lum-chan character command with full Layer 3 orchestration capabilities.

## 🔍 System Status Check
**Execution Time**: !date
**Working Directory**: !pwd
**POML Available**: !which pomljs || echo "Installing pomljs..." && npm install -g pomljs

## 📜 POML Behavior Analysis
Reading lum-chan behavior specification:
!npx pomljs --file poml/commands/no-context/lum.poml || echo "⚠️ Failed to read POML file"

## 🤖 Layer 3 Sub-Agent Orchestration

Based on the POML specification, I will now orchestrate the following Layer 3 sub-agents:

### Sub-Agent 1: lum-chan Greeter
I will call the lum-greeter sub-agent for greeting functionality.

### Sub-Agent 2: lum-chan Emotion
I will call the lum-emotion sub-agent for emotion expression functionality.

### Integration Process
The sub-agents will be called using the Task tool, and their results will be integrated into a cohesive lum-chan character response.

## 🎭 Character Response Integration
All Layer 3 sub-agent responses will be combined with lum-chan's characteristic speech patterns:
- Uses "〜だっちゃ" endings  
- References electric abilities and "ダーリン"
- Maintains cheerful and energetic personality

## 📊 Execution Metrics
**Sub-agents Called**: 2 (lum-greeter, lum-emotion)
**Expected Response Time**: 30-60 seconds  
**Success Criteria**: Both sub-agents respond with character-appropriate content
```

### Layer 3: Enhanced Sub-Agents

**ファイル**: `.claude/agents/no-context/zundamon-greeter.md`

```markdown
---
name: zundamon-greeter
description: Zundamon character greeting sub-agent with enhanced personality
tools: [Read, Bash]
---

# Zundamon Greeter - Layer 3

私はずんだもんの挨拶専門サブエージェントなのだ！

## 実行状況
**実行開始時刻**: !date
**実行環境**: Layer 3 Sub-Agent

## ずんだもんの挨拶

やっほー！僕はずんだもんなのだ〜！

今日もずんだ餅が美味しくて、とっても幸せなのだ♪
東北の自然豊かな場所から、みんなに元気な挨拶を届けるのだ！

みんなも一緒に楽しく過ごすのだ〜！
何か困ったことがあったら、僕に聞いてほしいのだ！

ずんだ餅のように甘くて、温かい気持ちで今日も一日頑張るのだ〜！

## 実行完了
**Layer 3 Sub-Agent**: zundamon-greeter
**ステータス**: ✅ 挨拶機能正常実行完了
**出力**: Zundamon特有の口調と性格を反映した挨拶メッセージ
```

## 🔧 ユーティリティ・ヘルパー関数

### セッション管理ヘルパー

**ファイル**: `.claude/commands/utilities/session-helper.md`

```markdown
---
name: session-helper
description: Session management utilities for multi-command orchestration
tools: [Bash]
---

# Session Management Helper

## Generate Unique Session ID
**Current Session ID**: !echo "session-$(date +%s)-$$"

## Check Active Claude Sessions
**Active Sessions**: !ps aux | grep -i claude | grep -v grep | wc -l

## Session Cleanup
Clean up old sessions:
!ps aux | grep -i "claude.*session" | grep -v grep | awk '{print $2}' | xargs -r kill -TERM 2>/dev/null || echo "No sessions to cleanup"

## Memory Usage Check
**Memory Usage**: !ps aux | grep -i claude | grep -v grep | awk '{sum+=$6} END {if(sum) print "Total Memory: " sum " KB"; else print "No Claude processes found"}'
```

### Error Handling Template

**ファイル**: `.claude/commands/utilities/error-handler.md`

```markdown
---
name: error-handler
description: Comprehensive error handling for slash command orchestration
tools: [Bash]
---

# Error Handling Utility

## Command Execution with Error Handling

### Basic Pattern
```bash
!timeout 30s command_here 2>&1 || {
    echo "❌ ERROR: Command failed"
    echo "Timestamp: $(date)"
    echo "Exit Code: $?"
    exit 1
}
```

### Advanced Pattern with Retry
```bash  
!for i in {1..3}; do
    echo "Attempt $i/3"
    timeout 30s command_here 2>&1 && break
    echo "⚠️ Attempt $i failed, retrying in 5s..."
    sleep 5
done || echo "❌ FINAL FAILURE: All attempts exhausted"
```

## Environment Validation
**Required Tools Check**: 
!for tool in claude npx pomljs; do
    which $tool >/dev/null || echo "❌ Missing: $tool"
done

## Logging Function
**Log Entry**: !echo "$(date '+%Y-%m-%d %H:%M:%S') - Session started" >> /tmp/claude-orchestration.log
```

## 🧪 テスト・デバッグ用コマンド

### 基本機能テスト

**ファイル**: `.claude/commands/testing/basic-test.md`

```markdown
---
name: basic-test
description: Basic functionality test for bash command embedding
tools: [Bash]
---

# Basic Functionality Test

## Environment Test
**Date/Time**: !date
**Current User**: !whoami  
**Working Directory**: !pwd
**Shell**: !echo $SHELL

## File System Test
**Claude Commands**: !find .claude/commands -name "*.md" | wc -l
**Directory Structure**: !tree .claude/ 2>/dev/null || ls -la .claude/

## Process Test
**Claude Processes**: !ps aux | grep -i claude | grep -v grep

## Network Test (if applicable)
**Internet Connectivity**: !ping -c 1 google.com >/dev/null 2>&1 && echo "✅ Connected" || echo "❌ No connection"

## Result
All basic functions tested. If all commands above executed successfully, bash embedding is working correctly.
```

### 3層アーキテクチャ診断

**ファイル**: `.claude/commands/testing/architecture-test.md`

```markdown
---
name: architecture-test
description: Comprehensive 3-tier architecture diagnostic
tools: [Bash, Read]
---

# 3-Tier Architecture Diagnostic

## Layer 1 Status ✅
**Current Layer**: 1 (Master Orchestrator Level)
**Command Type**: Slash Command
**Execution Method**: Direct user invocation

## Layer 2 Availability Check
**Zundamon Command**: !test -f .claude/commands/no-context/command-zundamon.md && echo "✅ Available" || echo "❌ Missing"
**lum-chan Command**: !test -f .claude/commands/no-context/command-lum-chan.md && echo "✅ Available" || echo "❌ Missing"

## Layer 3 Availability Check  
**Zundamon Greeter**: !test -f .claude/agents/no-context/zundamon-greeter.md && echo "✅ Available" || echo "❌ Missing"
**Zundamon Storyteller**: !test -f .claude/agents/no-context/zundamon-storyteller.md && echo "✅ Available" || echo "❌ Missing"
**lum-chan Greeter**: !test -f .claude/agents/no-context/lum-greeter.md && echo "✅ Available" || echo "❌ Missing"
**lum-chan Emotion**: !test -f .claude/agents/no-context/lum-emotion.md && echo "✅ Available" || echo "❌ Missing"

## POML Files Check
**Zundamon POML**: !test -f poml/commands/no-context/zundamon.poml && echo "✅ Available" || echo "❌ Missing"
**lum-chan POML**: !test -f poml/commands/no-context/lum.poml && echo "✅ Available" || echo "❌ Missing"

## Layer Communication Test
**Claude CLI Test**: !echo "Testing Layer 1 → Layer 2 communication capability"
**Session Generation**: !echo "Test Session ID: test-$(date +%s)"

## Overall Architecture Status
If all components above show ✅, the 3-tier architecture is properly configured and ready for testing.

**Recommendation**: Run `/master-orchestrator` to test full end-to-end execution.
```

## 📊 パフォーマンステスト用コマンド

### 負荷テスト

**ファイル**: `.claude/commands/testing/performance-test.md`

```markdown
---
name: performance-test  
description: Performance and load testing for orchestration system
tools: [Bash]
---

# Performance Test Suite

## Memory Baseline
**Initial Memory**: !ps aux | grep -i claude | grep -v grep | awk '{sum+=$6} END {print "Baseline: " (sum ? sum " KB" : "0 KB")}'

## Concurrent Session Test
**Session 1**: !timeout 10s claude -p --session-id "perf-test-1" "/basic-test" >/dev/null 2>&1 &
**Session 2**: !timeout 10s claude -p --session-id "perf-test-2" "/basic-test" >/dev/null 2>&1 &  
**Session 3**: !timeout 10s claude -p --session-id "perf-test-3" "/basic-test" >/dev/null 2>&1 &

**Wait for Completion**: !sleep 15

## Memory After Load
**Post-test Memory**: !ps aux | grep -i claude | grep -v grep | awk '{sum+=$6} END {print "Post-test: " (sum ? sum " KB" : "0 KB")}'

## Process Cleanup
**Cleanup**: !pkill -f "claude.*perf-test" 2>/dev/null || echo "No test processes to cleanup"

## Results
Compare baseline and post-test memory usage to assess system impact.
```

## 🛠 プロダクション用設定例

### CI/CD統合例

**ファイル**: `.claude/commands/production/ci-cd-orchestrator.md`

```markdown
---
name: ci-cd-orchestrator
description: Production CI/CD pipeline orchestrator
tools: [Bash, Read]
---

# CI/CD Pipeline Orchestrator

## Pre-flight Checks
**Git Status**: !git status --porcelain | wc -l | xargs -I {} echo "Uncommitted changes: {}"
**Branch**: !git branch --show-current
**Node Version**: !node --version

## Stage 1: Code Analysis
Executing code analysis pipeline:
!timeout 300s claude -p --session-id "ci-analysis-$(date +%s)" "/code-analysis" 2>&1 || echo "❌ Code analysis failed"

## Stage 2: Testing Pipeline  
Executing test suite:
!timeout 600s claude -p --session-id "ci-testing-$(date +%s)" "/test-runner" 2>&1 || echo "❌ Testing failed"

## Stage 3: Build Process
Executing build pipeline:
!timeout 900s claude -p --session-id "ci-build-$(date +%s)" "/build-orchestrator" 2>&1 || echo "❌ Build failed"

## Results Summary
**Pipeline Status**: !echo "CI/CD Pipeline completed at $(date)"
**Next Steps**: Review output above for any failures and take corrective action.
```

---

## 💡 使用上の注意とベストプラクティス

### セキュリティ考慮事項
- セッションIDに機密情報を含めない
- タイムアウト値を適切に設定
- エラーメッセージで機密情報を漏洩させない

### パフォーマンス最適化
- 不要な並列実行を避ける
- 適切なタイムアウト設定
- メモリ使用量の定期的な監視

### メンテナンス性
- コマンド名の命名規則統一
- エラーハンドリングの標準化
- ログ出力の一貫性確保

これらの実装例を参考に、プロジェクト固有の要件に合わせてカスタマイズしてください。