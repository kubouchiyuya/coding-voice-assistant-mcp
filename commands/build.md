---
description: "実装フェーズ — コード分析、合議、実装を実行"
argument-hint: "<実装する機能の説明>"
allowed-tools: [Bash, Read, Write, Edit, Glob, Grep, WebSearch, Agent]
---

# /build コマンド

既存コード分析→合議→実装を実行する。

## フロー

1. `explorer` で既存コード分析
2. 合議チームで実装方針決定
3. `implementer` で実装
4. 🔊 進捗を音声報告

notify.sh パス: `${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh`
