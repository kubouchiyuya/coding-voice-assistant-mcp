---
name: deploy-helper
description: "This skill should be used when the user says '公開したい', 'デプロイ', 'deploy', 'ship it', '本番公開', 'リリース', 'release', 'ステージング', 'staging', 'Vercel', 'Netlify', 'Cloudflare Pages', '公開する', 'push to production'. Guides deployment to major hosting platforms with prerequisite checks, build verification, and voice status reports."
version: 1.0.0
---

# デプロイヘルパースキル — 安全なデプロイを音声でガイド

Web アプリケーションを各種ホスティングプラットフォームにデプロイする作業を支援するスキル。前提条件の確認、ビルドの検証、デプロイの実行、結果のURL報告までを一貫してガイドし、各ステップで音声フィードバックを行う。

## CLI パス

```
NOTIFY="${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh"
```

## デプロイフロー（6ステップ）

### ステップ1: 宣言と確認

```bash
$NOTIFY "デプロイの準備を始めます。まず前提条件を確認しますね"
```

### ステップ2: 前提条件チェック

デプロイ前に必ず以下を確認する：

#### 共通チェックリスト
```bash
# Git の状態を確認
git status
git log --oneline -5

# 未コミットの変更がないか
git diff --stat

# 現在のブランチ
git branch --show-current

# package.json の存在とビルドスクリプト
cat package.json | grep -A5 '"scripts"'

# 環境変数の確認
ls -la .env* 2>/dev/null
```

チェック結果を音声で報告：
```bash
$NOTIFY "前提条件を確認しました。未コミットの変更が{N}件あります" # 問題あり
# または
$NOTIFY "前提条件OKです。ビルドに進みます" # 問題なし
```

**未コミットの変更がある場合:**
```bash
$NOTIFY "未コミットの変更があります。先にコミットしますか？" confirm
```

### ステップ3: ビルド検証

```bash
$NOTIFY "ビルドを実行します"
```

```bash
# 依存関係のインストール
npm ci

# リンター・型チェック（該当する場合）
npm run lint 2>/dev/null
npx tsc --noEmit 2>/dev/null

# テスト実行
npm test 2>/dev/null

# プロダクションビルド
npm run build
```

ビルド結果を報告：
```bash
$NOTIFY "ビルド成功です。デプロイに進みます" complete
# または
$NOTIFY "ビルドエラーです。修正が必要です" error
```

### ステップ4: プラットフォーム選択とデプロイ

ユーザーが指定しない場合は、プロジェクト構成から推奨プラットフォームを判定する。

### ステップ5: デプロイ実行

プラットフォーム別の手順に従ってデプロイを実行する（後述）。

### ステップ6: 結果報告

```bash
$NOTIFY "デプロイが完了しました。URLをチャットに表示します" complete
```

## プラットフォーム別ガイド

### Vercel

**推奨プロジェクト:** Next.js、React、Vue、Svelte、静的サイト

**初回セットアップ:**
```bash
# Vercel CLI インストール
npm i -g vercel

# ログイン
vercel login

# プロジェクト連携
vercel link
```

**デプロイ実行:**
```bash
# プレビューデプロイ（ステージング）
vercel

# 本番デプロイ
vercel --prod
```

**環境変数の設定:**
```bash
# 環境変数をVercelに追加
vercel env add VARIABLE_NAME production
```

**確認事項:**
- `vercel.json` の設定（リダイレクト、ヘッダー等）
- ビルドコマンドとアウトプットディレクトリの自動検出
- サーバーレスファンクションの制限（10秒、50MB）

### Netlify

**推奨プロジェクト:** 静的サイト、Gatsby、Hugo、Astro、JAMstack

**初回セットアップ:**
```bash
# Netlify CLI インストール
npm i -g netlify-cli

# ログイン
netlify login

# プロジェクト連携
netlify init
```

**デプロイ実行:**
```bash
# ドラフトデプロイ（プレビュー）
netlify deploy

# 本番デプロイ
netlify deploy --prod
```

**確認事項:**
- `netlify.toml` の設定
- ビルドコマンド: `npm run build`
- パブリッシュディレクトリ: `dist` / `build` / `out` / `.next`
- リダイレクトルール（SPA の場合 `_redirects` ファイル）
- Netlify Functions（`netlify/functions/` ディレクトリ）

### Cloudflare Pages

**推奨プロジェクト:** 静的サイト、フルスタック（Workers と連携）

**初回セットアップ:**
```bash
# Wrangler CLI インストール
npm i -g wrangler

# ログイン
wrangler login

# Pages プロジェクト作成
wrangler pages project create {プロジェクト名}
```

**デプロイ実行:**
```bash
# ビルド出力ディレクトリをデプロイ
wrangler pages deploy {出力ディレクトリ}
```

**確認事項:**
- `wrangler.toml` の設定
- ビルドアウトプットディレクトリの指定
- Workers の連携設定
- カスタムドメインの設定

## プラットフォーム選択の判定基準

| 条件 | 推奨 |
|------|------|
| Next.js プロジェクト | Vercel（最適化あり） |
| 静的サイト（Hugo/Astro） | Netlify or Cloudflare Pages |
| サーバーレスファンクション重視 | Vercel or Cloudflare Workers |
| 大量の帯域幅が必要 | Cloudflare Pages（無制限） |
| フォーム機能が必要 | Netlify（Forms 機能あり） |
| エッジコンピューティング | Cloudflare Pages + Workers |

プロジェクトを分析した上で推奨：
```bash
$NOTIFY "Next.jsプロジェクトですね。Vercelが最適です。Vercelでデプロイしますか？" confirm
```

## デプロイ前の安全チェック

本番デプロイ前に以下を自動チェックする：

1. **シークレットの漏洩チェック**
```bash
# .env ファイルが .gitignore に含まれているか
grep -q ".env" .gitignore && echo "OK" || echo "WARNING: .env not in .gitignore"

# ハードコードされた秘密情報がないか
grep -rn "sk-\|password\s*=\|secret\s*=" src/ --include="*.ts" --include="*.js" --include="*.tsx" --include="*.jsx"
```

2. **ビルド成果物の確認**
```bash
# ビルド出力のサイズ確認
du -sh dist/ build/ .next/ out/ 2>/dev/null
```

3. **ブランチの確認**
```bash
# main/master ブランチからのデプロイか
BRANCH=$(git branch --show-current)
```

## ロールバック手順

デプロイ後に問題が発生した場合：

```bash
$NOTIFY "問題が発生しました。ロールバックしますか？" confirm
```

### Vercel
```bash
# 直前のデプロイ一覧
vercel ls

# 特定のデプロイをプロモート
vercel promote {deployment-url}
```

### Netlify
```bash
# デプロイ一覧
netlify deploys

# 特定のデプロイを本番に復元
netlify deploy --prod --dir={前回のビルドディレクトリ}
```

### Cloudflare Pages
```bash
# ダッシュボードからロールバック、またはGit revert後に再デプロイ
wrangler pages deploy {前回のビルドディレクトリ}
```

## 音声報告のルール

- デプロイ**開始前**に「デプロイの準備を始めます」と宣言
- 前提条件に**問題があれば**即座に報告
- **ビルド結果**を成功/失敗で報告
- デプロイ**完了時**にURLを含めて complete で報告
- **ロールバック**が必要な場合は confirm で確認を求める
- 本番デプロイは**必ず confirm** で確認を取ってから実行する
