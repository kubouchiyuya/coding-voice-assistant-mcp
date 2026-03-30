---
name: security-audit
description: "This skill should be used when the user says 'セキュリティチェック', '脆弱性', 'security audit', 'セキュリティ監査', 'XSS', 'CSRF', 'SQLインジェクション', 'auth check', '認証チェック', '安全性確認', 'dependency audit', 'npm audit', '秘密情報漏れ', 'secret leak', 'OWASP'. Performs comprehensive security scanning based on OWASP Top 10, dependency auditing, and auth logic review with voice reporting."
version: 1.0.0
---

# セキュリティ監査スキル — OWASP準拠のコード診断

Webアプリケーションのセキュリティを体系的に診断するスキル。OWASP Top 10 をベースにコードの脆弱性を検出し、依存パッケージの監査、認証ロジックのレビュー、秘密情報の漏洩チェックを実行する。診断の進捗と結果を音声で報告する。

## CLI パス

```
NOTIFY="${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh"
```

## 監査フロー（5ステップ）

### ステップ1: 監査開始の宣言

```bash
$NOTIFY "セキュリティ監査を開始します。コードを全体的にスキャンします"
```

### ステップ2: 依存パッケージの監査

```bash
# npm の脆弱性チェック
npm audit

# 詳細レポート
npm audit --json

# 自動修正可能な脆弱性を修正
npm audit fix
```

結果報告：
```bash
$NOTIFY "依存パッケージを確認しました。{N}件の脆弱性が見つかりました" error
# または
$NOTIFY "依存パッケージに問題はありませんでした"
```

### ステップ3: 秘密情報の漏洩チェック

```bash
# .env ファイルが .gitignore に含まれているか
grep -q "\.env" .gitignore && echo "OK" || echo "CRITICAL: .env not in .gitignore"

# Git 履歴に秘密情報が含まれていないか
git log --all --diff-filter=A -- "*.env" ".env.*"

# ハードコードされた秘密情報を検索
grep -rn --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" \
  -E "(password|secret|api_key|apikey|access_token|private_key)\s*[:=]" src/

# APIキーのパターン検索
grep -rn --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" \
  -E "(sk-[a-zA-Z0-9]{20,}|ghp_[a-zA-Z0-9]{36}|AKIA[A-Z0-9]{16})" src/
```

### ステップ4: OWASP Top 10 チェック

以下の各項目についてコードをレビューする。

#### A01: アクセス制御の不備（Broken Access Control）

**チェック内容:**
- 認可チェックがすべてのエンドポイントに存在するか
- ユーザーが他ユーザーのリソースにアクセスできないか（IDOR）
- 管理者機能にロールチェックがあるか
- API エンドポイントに認証ミドルウェアが適用されているか

**検索パターン:**
```bash
# 認証ミドルウェアなしのルート定義を検索
grep -rn "router\.\(get\|post\|put\|delete\)" src/ --include="*.ts" --include="*.js"

# 認可チェックの存在確認
grep -rn "isAdmin\|isAuthenticated\|authorize\|requireAuth\|middleware" src/ --include="*.ts" --include="*.js"
```

#### A02: 暗号化の失敗（Cryptographic Failures）

**チェック内容:**
- パスワードがハッシュ化されているか（bcrypt, argon2）
- 通信がHTTPS強制か
- JWTシークレットが十分な強度か
- 機密データが平文で保存されていないか

**検索パターン:**
```bash
# パスワード処理の確認
grep -rn "password" src/ --include="*.ts" --include="*.js" -i

# ハッシュ関数の使用確認
grep -rn "bcrypt\|argon2\|scrypt\|pbkdf2" src/ --include="*.ts" --include="*.js"
```

#### A03: インジェクション（Injection）

**チェック内容:**
- SQLクエリにパラメータバインディングを使用しているか（文字列結合による組み立ては禁止）
- ユーザー入力をHTMLに直接埋め込んでいないか（XSS）
- コマンドインジェクションの可能性がないか
- NoSQLインジェクションの対策があるか

**検索パターン:**
```bash
# SQL文字列結合を検索
grep -rn "SELECT.*+\|INSERT.*+\|UPDATE.*+\|DELETE.*+" src/ --include="*.ts" --include="*.js"

# dangerouslySetInnerHTML の使用
grep -rn "dangerouslySetInnerHTML\|innerHTML\|v-html" src/ --include="*.tsx" --include="*.jsx" --include="*.vue"

# eval の使用
grep -rn "eval(\|Function(\|exec(" src/ --include="*.ts" --include="*.js"
```

#### A04: 安全でない設計（Insecure Design）

**チェック内容:**
- レート制限が実装されているか
- 入力バリデーションが存在するか
- エラーメッセージが詳細すぎないか（スタックトレースの露出）
- CORS設定が適切か

**検索パターン:**
```bash
# CORS設定の確認
grep -rn "cors\|Access-Control" src/ --include="*.ts" --include="*.js"

# レート制限の確認
grep -rn "rateLimit\|rate-limit\|throttle" src/ --include="*.ts" --include="*.js"
```

#### A05: セキュリティの設定ミス（Security Misconfiguration）

**チェック内容:**
- デバッグモードが本番で無効か
- 不要なHTTPメソッドが許可されていないか
- セキュリティヘッダーが設定されているか（CSP, X-Frame-Options 等）
- デフォルトのクレデンシャルが残っていないか

**検索パターン:**
```bash
# デバッグ設定の確認
grep -rn "debug.*true\|NODE_ENV.*development" src/ --include="*.ts" --include="*.js"

# セキュリティヘッダーの確認
grep -rn "helmet\|Content-Security-Policy\|X-Frame-Options\|X-Content-Type" src/ --include="*.ts" --include="*.js"
```

#### A06: 脆弱なコンポーネント（Vulnerable Components）

**チェック内容:** npm audit の結果を参照（ステップ2で実施済み）

#### A07: 認証の不備（Identification and Authentication Failures）

**チェック内容:**
- パスワードポリシーが実装されているか（最小長、複雑さ）
- ブルートフォース対策（アカウントロック、遅延）
- セッション管理が適切か（有効期限、再生成）
- 多要素認証の対応状況

**検索パターン:**
```bash
# セッション・JWT設定の確認
grep -rn "session\|jwt\|expiresIn\|maxAge" src/ --include="*.ts" --include="*.js"

# パスワードバリデーション
grep -rn "password.*length\|minLength\|strongPassword" src/ --include="*.ts" --include="*.js"
```

#### A08: ソフトウェアとデータの整合性（Software and Data Integrity）

**チェック内容:**
- CI/CDパイプラインの整合性
- 依存関係のロックファイルの存在（package-lock.json, yarn.lock）
- サプライチェーン攻撃への対策

#### A09: ログと監視の不備（Logging and Monitoring Failures）

**チェック内容:**
- ログイン失敗のログが記録されているか
- 重要操作のログが記録されているか
- ログに機密情報が含まれていないか

**検索パターン:**
```bash
# ログ記録の確認
grep -rn "console\.log\|logger\.\(info\|warn\|error\)" src/ --include="*.ts" --include="*.js"
```

#### A10: SSRF（Server-Side Request Forgery）

**チェック内容:**
- ユーザー入力由来のURLに対するHTTPリクエストがないか
- URLのバリデーション・ホワイトリスト

**検索パターン:**
```bash
# 外部リクエストの確認
grep -rn "fetch(\|axios\.\|http\.request\|got(" src/ --include="*.ts" --include="*.js"
```

### ステップ5: レポート生成と報告

```bash
$NOTIFY "セキュリティ監査が完了しました。レポートを表示します" complete
```

## レポートフォーマット

監査結果を以下の形式で出力する：

```
## セキュリティ監査レポート

### 概要
- 監査日時: {日時}
- 対象: {プロジェクト名}
- 検出された問題: {件数}
  - 重大 (Critical): {N}件
  - 高 (High): {N}件
  - 中 (Medium): {N}件
  - 低 (Low): {N}件

### 検出された問題

#### [重大度] 問題タイトル
- OWASP分類: A0X
- 該当ファイル: {ファイルパス}:{行番号}
- 説明: {問題の説明}
- 推奨修正: {修正方法}
- コード例: {修正前→修正後}

### 推奨アクション（優先度順）
1. {最も緊急な対応}
2. {次に重要な対応}
3. ...
```

## 重大度の分類基準

| 重大度 | 基準 | 例 |
|--------|------|-----|
| Critical | データ漏洩・完全な乗っ取りの可能性 | SQLインジェクション、認証バイパス |
| High | 重要機能への影響 | XSS（格納型）、CSRF、権限昇格 |
| Medium | 限定的な影響 | XSS（反射型）、設定ミス、情報漏洩 |
| Low | 最小限の影響 | 軽微な設定問題、非推奨ライブラリ |

## 音声報告のルール

- 監査**開始時**に「セキュリティ監査を開始します」と宣言
- 各カテゴリの確認ごとに問題があれば即座に error で報告
- **重大な脆弱性**を発見した場合は即座に音声で警告
- 監査**完了時**に件数を含めた要約を complete で報告
- 問題がなかった場合も「問題はありませんでした」と明示的に報告
