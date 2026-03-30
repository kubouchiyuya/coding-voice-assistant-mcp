---
description: "戦略・設計フェーズ — 要件整理、技術選定（合議）、システム設計、UI設計"
argument-hint: "<作りたいものの説明>"
allowed-tools: [Bash, Read, Glob, Grep, WebSearch, WebFetch, Agent, AskUserQuestion]
---

# /plan コマンド

要件定義→技術選定（合議）→設計までを実行する。

## フロー

1. `strategist` エージェントで要件整理（非エンジニア対応）
2. 合議チーム5体で技術選定
3. `architect` + `ux-designer` で設計
4. 🔊 各段階の結果を音声報告

notify.sh パス: `${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh`
