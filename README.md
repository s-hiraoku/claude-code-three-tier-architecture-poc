# Claude Code Three Tier Architecture POC テスト用コマンド

## 概要

Claude Code Three Tier Architecture POC の 3 層コマンド体系をテストするためのダミーコマンドシステムです。

## 構成

### 第 1 層: Orchestrator

- `orchestrator` - メインのオーケストレーターコマンド
- `orchestrator.poml` - オーケストレーターの動作定義

### 第 2 層: カスタムコマンド

- `command-zundamon` - ずんだもんキャラクターコマンド
- `command-lum-chan` - ラムちゃんキャラクターコマンド

### 第 3 層: POML 定義

- `zundamon.poml` - ずんだもんの口調と性格定義
- `lum.poml` - ラムちゃんの口調と性格定義

## 実行方法

Claude Code でカスタムスラッシュコマンドとして実行：

```bash
/orchestrator
```

## 期待される動作

1. `orchestrator` が実行される
2. `orchestrator.poml` が読み込まれる
3. `command-zundamon` が呼び出され、ずんだもんが話す
4. `command-lum-chan` が呼び出され、ラムちゃんが話す

両方のキャラクターの発言が表示されることで、3 層構造の動作を確認できます。
