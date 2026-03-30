---
name: error-doctor
description: "This skill should be used when the user says 'エラー出た', '動かない', 'fix this', 'なぜ壊れた', 'バグ', 'bug', 'TypeError', 'エラー直して', 'クラッシュ', 'なんで動かない', 'ビルドエラー', 'build failed', 'コンパイルエラー', '赤くなった', 'undefined', 'null reference'. Provides step-by-step error diagnosis, root cause analysis, and automated fix application with voice feedback."
version: 1.0.0
---

# エラードクタースキル — 診断・原因特定・自動修復

エラーや不具合が発生した際に、体系的な診断プロセスで原因を特定し、修正を提案・適用するスキル。診断の進捗と結果を音声でリアルタイム報告する。

## CLI パス

```
NOTIFY="${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh"
```

## 診断フロー（5ステップ）

エラーが報告されたら、必ず以下の順序で対応する。各ステップで音声報告を行う。

### ステップ1: 症状の確認と音声宣言

```bash
$NOTIFY "エラーを確認します。まず症状を見てみましょう"
```

- エラーメッセージの全文を確認する
- エラーの種類を分類する（下記の分類表を参照）
- 発生タイミングを確認する（ビルド時？実行時？特定操作時？）

### ステップ2: エラー分類

| カテゴリ | 典型的なエラー | よくある原因 |
|----------|----------------|--------------|
| 型エラー | TypeError, type mismatch | 型の不一致、null/undefined アクセス |
| 構文エラー | SyntaxError, unexpected token | タイポ、括弧の不一致、セミコロン抜け |
| 参照エラー | ReferenceError, not defined | 変数未宣言、スコープ外、import 漏れ |
| モジュールエラー | Module not found, Cannot resolve | パッケージ未インストール、パス間違い |
| ネットワークエラー | CORS, 403, 404, 500, timeout | API設定、認証、サーバー障害 |
| 環境エラー | env undefined, config missing | 環境変数未設定、設定ファイル不備 |
| 依存関係エラー | peer dependency, version conflict | パッケージバージョンの競合 |
| ビルドエラー | webpack/vite/tsc error | 設定ファイルの問題、互換性 |

### ステップ3: 原因の深掘り

```bash
$NOTIFY "原因を調べています。少しお待ちください"
```

原因調査の手順：

1. **エラーメッセージを正確に読む** — ファイル名、行番号、スタックトレースを確認
2. **該当コードを確認する** — エラーが指す箇所の前後10行を読む
3. **変更履歴を確認する** — `git diff` で直近の変更を確認
4. **依存関係を確認する** — `package.json` や lock ファイルの状態
5. **環境を確認する** — Node.js バージョン、環境変数、OS

### ステップ4: 原因報告と修正提案

```bash
$NOTIFY "原因がわかりました。{原因の要約}です" error
```

修正案を提示する際のフォーマット：

> **原因:** {根本原因の説明}
>
> **影響範囲:** {影響を受けるファイル・機能}
>
> **修正案A:** {推奨される修正方法}
> **修正案B:** {代替の修正方法}（該当する場合）
>
> **推奨:** {どちらがよいか理由とともに}

### ステップ5: 修正適用と確認

```bash
$NOTIFY "修正を適用します"
```

修正を適用した後、必ず以下を実行する：

1. **型チェック**（TypeScript の場合）: `npx tsc --noEmit`
2. **リンター**: `npx eslint {修正ファイル}`
3. **テスト**: `npm test` または該当テストファイルを実行
4. **ビルド確認**: `npm run build`

```bash
$NOTIFY "修正が完了しました。テストもパスしています" complete
# または
$NOTIFY "修正しましたが、別のエラーが出ています。続けて対応します" error
```

## よくあるエラーの即座修正パターン

### パターン1: モジュールが見つからない
```
Error: Cannot find module 'xxx'
```
**診断手順:**
```bash
# パッケージがインストールされているか確認
ls node_modules/xxx 2>/dev/null || echo "未インストール"
# package.json に記載があるか確認
grep "xxx" package.json
```
**修正:** `npm install xxx` を実行

### パターン2: 型の不一致（TypeScript）
```
Type 'string' is not assignable to type 'number'
```
**診断手順:** エラーが指す行の変数の型定義と、代入されている値を比較
**修正:** 型定義の修正または値の変換を適用

### パターン3: null/undefined アクセス
```
TypeError: Cannot read properties of undefined (reading 'xxx')
```
**診断手順:** 変数が undefined になる経路を特定（API レスポンス、オプショナルな props 等）
**修正:** オプショナルチェーン（?.）やデフォルト値の追加

### パターン4: CORS エラー
```
Access-Control-Allow-Origin header is missing
```
**診断手順:** API サーバーの CORS 設定を確認
**修正:** サーバー側で適切な CORS ヘッダーを設定。開発時はプロキシ設定も検討

### パターン5: 環境変数が未定義
```
Error: Required environment variable XXX is not set
```
**診断手順:** `.env` ファイルの有無と内容を確認。`.env.example` との差分を確認
**修正:** 必要な環境変数を `.env` ファイルに追加

### パターン6: ポート競合
```
Error: listen EADDRINUSE: address already in use :::3000
```
**診断手順:**
```bash
lsof -i :3000
```
**修正:** 既存プロセスを終了するか、別のポートを使用

## 複合エラーへの対処

エラーが連鎖している場合の対処：

1. **最初のエラーから対処する** — 後続のエラーは最初のエラーが原因の可能性が高い
2. **1つ修正するごとに再確認する** — 全部まとめて直そうとしない
3. **修正の影響を検証する** — 新たなエラーが発生していないか確認

```bash
$NOTIFY "エラーが複数あります。まず最初のエラーから対処します"
# 修正後
$NOTIFY "1つ目のエラーを修正しました。残りのエラーを確認します"
```

## 音声報告のルール

- 診断**開始時**に必ず「エラーを確認します」と宣言
- **原因特定時**に error タイプで報告
- **修正完了時**に complete タイプで報告
- 技術的な詳細はテキストで、**要約を音声で**伝える
- エラーメッセージそのものは音声で読み上げない（長すぎるため）

## 緊急度の判定

| 緊急度 | 状態 | 対応 |
|--------|------|------|
| 高 | 本番環境で障害発生 | 即座にロールバック提案 |
| 中 | ビルド・テストが通らない | 原因特定と修正を優先 |
| 低 | 警告・非推奨通知 | 情報提供のみ。作業中断しない |
