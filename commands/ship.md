---
description: "デプロイフェーズ — ビルド確認、デプロイ先選定、公開、URL報告"
argument-hint: "[vercel|netlify|cloudflare]"
allowed-tools: [Bash, Read, Glob, Grep, Agent, AskUserQuestion]
---

# /ship コマンド

デプロイを実行する。必ずユーザー承認を得てから実行。

## フロー

1. ビルド確認（npm run build）
2. デプロイ先選定（引数指定 or 音声で確認）
3. 🔊 「デプロイしますか？」と確認
4. deploy-helper スキルでデプロイ実行
5. 🔊 「完了しました。URLは〇〇です」と報告

notify.sh パス: `${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh`
