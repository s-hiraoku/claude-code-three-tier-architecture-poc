# POML Multiple Task Elements Documentation

## Overview

This document confirms that POML (Prompt Orchestration Markup Language) supports multiple `<task>` elements within a single POML file based on source code analysis.

## Source Code Evidence

**Reference**: [Microsoft POML GitHub Repository - instructions.tsx](https://github.com/microsoft/poml/blob/da535a7c5a36b10479caca10ea9df2466123f7d9/packages/poml/components/instructions.tsx#L76)

## Supported Syntax

### Multiple Task Elements

```xml
<poml>
  <task>
    First task description
    <list>
      <item>Step 1 of first task</item>
      <item>Step 2 of first task</item>
    </list>
  </task>
  
  <task>
    Second task description
    <list>
      <item>Step 1 of second task</item>
      <item>Step 2 of second task</item>
    </list>
  </task>
</poml>
```

### Sequential Execution

Tasks are executed in the order they appear in the document:

1. **First Task**: Executes all items in its list sequentially
2. **Second Task**: Executes after the first task completes
3. **Subsequent Tasks**: Follow the same pattern

## Implementation in Claude Code POC

### with-context/orchestrator.poml Example

```xml
<poml>
  <meta minVersion="0.0.8" />
  
  <role>with-context オーケストレーター - Layer 1コンテキスト管理エージェント</role>
  
  <task>
    以下のステップを順番に実行してください：
    <list>
      <item>コンテキスト「{{CONTEXT}}」を取得してcontext変数に設定</item>
      <item>`.claude/commands/with-context/command-zundamon.md`を読み込んで、その指示に従って実行</item>
      <item>zundamonからの応答を受け取り表示</item>
      <item>応答をzundamon_response変数に保存</item>
    </list>
  </task>
  
  <task>
    以下のステップを順番に実行してください：
    <list>
      <item>コンテキスト「{{CONTEXT}}」を取得してcontext変数に設定</item>
      <item>`.claude/commands/with-context/command-lum-chan.md`を読み込んで、その指示に従って実行</item>
      <item>lumからの応答を受け取り表示</item>
      <item>応答をlum_response変数に保存</item>
    </list>
  </task>
  
  <let name="context" value="{{CONTEXT}}"/>
</poml>
```

## Benefits

### 1. **Clear Separation of Concerns**
- Each agent's execution is isolated in its own task
- Individual task failure doesn't affect other tasks

### 2. **Sequential Display**
- Each task's output is displayed when completed
- No need to wait for all tasks to complete before seeing results

### 3. **Maintainability**
- Easy to add, remove, or modify individual agent tasks
- Clear structure for debugging

## Validation Status

✅ **Confirmed**: Multiple `<task>` elements are officially supported by POML  
✅ **Source**: Microsoft POML GitHub repository source code analysis  
✅ **Implementation**: Successfully used in Claude Code POC architecture  

## Date

Created: 2025-01-30  
Last Updated: 2025-01-30  
Validated Against: POML source code (commit da535a7)