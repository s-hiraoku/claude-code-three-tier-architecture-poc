---
name: simple-orchestrator
description: Simple hierarchical workflow orchestration without complex hacks
tools: [Bash, Read]
---

# Simple Orchestrator - Clear Hierarchical Flow

I am a simple orchestrator that provides clear hierarchical workflow structure without complex technical hacks.

## 🎯 Workflow Overview
```
Layer 1: This orchestrator (planning & coordination)
Layer 2: Character-specific workflows (execution phases)  
Layer 3: Specific tasks (actual work)
```

## 📋 Phase 1: Planning & Setup
**Current Status**: !date
**Working Directory**: !pwd
**Available Resources**: !ls -la .claude/commands/no-context/ | grep -c ".md" | xargs -I {} echo "{} commands available"

**Workflow Plan**:
1. Execute Zundamon character workflow
2. Execute lum-chan character workflow
3. Integrate and present results

## 🤖 Phase 2: Character Workflow Execution

### Zundamon Character Phase
**Instruction**: "Please execute the Zundamon character workflow by running `/command-zundamon`"

**Expected Outcome**: 
- Zundamon greeting functionality
- Zundamon storytelling functionality
- Character-specific responses with "〜なのだ" patterns

### lum-chan Character Phase  
**Instruction**: "Please execute the lum-chan character workflow by running `/command-lum-chan`"

**Expected Outcome**:
- lum-chan greeting functionality
- lum-chan emotion expression functionality  
- Character-specific responses with "〜だっちゃ" patterns

## 📊 Phase 3: Integration & Results

### Expected Integration Pattern
1. **Zundamon Results**: Character greetings + storytelling
2. **lum-chan Results**: Character greetings + emotions
3. **Combined Output**: Integrated character interactions

### Manual Execution Steps
Since automated slash command calling has limitations, please:

1. **Run this orchestrator** to see the plan
2. **Manually execute** `/command-zundamon`  
3. **Manually execute** `/command-lum-chan`
4. **Review results** from both commands

## 🎉 Success Criteria
- ✅ Clear workflow structure visible
- ✅ Character-specific processing phases defined
- ✅ Integration plan established
- ✅ Manual execution path provided

---

## 💡 Why This Works

This approach provides **hierarchical flow structure** without technical complexity:

- **Layer 1**: This planning/coordination command
- **Layer 2**: Character-specific commands (run manually) 
- **Layer 3**: Sub-agents (called by Layer 2 via Task tool)

**Simple, clear, and actually functional!**