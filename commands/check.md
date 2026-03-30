---
description: "テスト・レビューフェーズ — テスト実行、全方位レビュー、自動修正"
argument-hint: "[ファイルやディレクトリ]"
allowed-tools: [Bash, Read, Write, Edit, Glob, Grep, Agent]
---

# /check コマンド

テスト→レビュー→修正→再テストを実行する。

## フロー

1. `tester` でテスト作成・実行
2. `reviewer` で全方位レビュー（セキュリティ/パフォーマンス/保守性/a11y）
3. 問題検出 → 合議で方針決定 → `implementer` で修正 → 再テスト
4. 🔊 結果を音声報告

notify.sh パス: `${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh`
