---
description: "音声通知の管理 - テスト再生、ON/OFF切替、設定変更"
argument-hint: "[test|on|off|status|speak <message>|settings]"
allowed-tools: [Bash]
---

# /voice コマンド

音声通知の管理。NOTIFY パスは `${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh` を使用。

### 引数なし or `test`
```bash
${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh "テスト再生です。音声通知が正常に動作しています" complete
```

### `on` / `off` / `status`
```bash
${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh on
${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh off
${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh status
```

### `speak <message>`
```bash
${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh "<message>"
```

### `settings`
```bash
${CLAUDE_PLUGIN_ROOT}/scripts/settings.sh
```
