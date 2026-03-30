---
description: "フルサイクル・コーディングアシスタント — 戦略→設計→実装→テスト→レビュー→デプロイを自動実行"
argument-hint: "[auto|step] <作りたいものの説明>"
allowed-tools: [Bash, Read, Write, Edit, Glob, Grep, WebSearch, WebFetch, Agent, AskUserQuestion]
---

# /assist コマンド — フルサイクル自動実行

ユーザーの「作りたいもの」を受け取り、全工程を音声ガイド付きで自動実行する。

## モード

- `auto`（デフォルト）: 重要判断時のみ停止。それ以外は自動進行。
- `step`: 各フェーズ完了時に音声で報告し、承認を得てから次へ進む。

## 実行フロー

### Phase 1: 要件定義（strategist エージェント）

1. `strategist` エージェントを起動し、ユーザーの要望を整理
2. 非エンジニアには平易な言葉で質問する
3. 🔊 音声で要件サマリーを報告
4. 初心者モード: Yes/Noで答えられる質問のみ提示
5. 詳細モード: 選択肢を番号で提示

### Phase 2: 技術選定（合議 — 5体全員）

1. `builder-alpha` と `builder-beta` を**並列**で起動し、それぞれ提案を出す
2. `critic` を起動し、両提案の問題点を指摘
3. `mediator` を起動し、criticの指摘の妥当性を評価
4. `lead` が最終判断を下す
5. 🔊 音声で合議結果を報告
6. `auto`モード: そのまま進行 / `step`モード: ユーザー承認待ち

### Phase 3: 設計（architect + ux-designer 並列）

1. `architect` エージェントでシステム設計
2. `ux-designer` エージェントでUI/UX設計
3. デザイントークン（4px基準、グレースケール+アクセント）を定義
4. 🔊 音声で設計サマリーを報告

### Phase 4: 実装（implementer、合議で監督）

1. `explorer` で既存コード分析（既存プロジェクトの場合）
2. `implementer` エージェントを起動して実装
3. 大きな判断が必要な場面で合議チームを自動召集
4. 🔊 進捗を音声で定期報告

### Phase 5: テスト・レビュー（tester + reviewer 並列）

1. `tester` エージェントでテスト作成・実行
2. `reviewer` エージェントで全方位レビュー（セキュリティ/パフォーマンス/保守性/アクセシビリティ）
3. 問題検出時: 合議チームで修正方針決定 → `implementer` で自動修正 → 再テスト
4. 🔊 テスト結果を音声で報告

### Phase 6: デプロイ（ユーザー承認必須）

1. 🔊 「全テスト合格しました。デプロイしますか？」と音声で確認
2. ユーザー承認後、`deploy-helper` スキルでデプロイ
3. 🔊 「デプロイ完了しました。URLは○○です」と音声で報告

## 音声通知タイミング

全フェーズの開始・完了時に必ず音声で報告する。
notify.sh のパスは `${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh` を使用。

```bash
NOTIFY="${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh"
$NOTIFY "フェーズ1、要件定義を開始します" info
```

## 合議の結果表示

デフォルト: 結論のみ表示（コンテキスト節約）。
ユーザーが「議論見せて」と言った場合のみ、全議論を表示する。
