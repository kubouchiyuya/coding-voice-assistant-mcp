---
name: ui-generator
description: "This skill should be used when the user says 'こんな画面作って', 'UIコード', 'ページ生成', 'generate UI', 'コンポーネント作って', 'ボタン作って', 'フォーム作って', 'ダッシュボード作って', 'ランディングページ', 'LP作って', 'テーブル作って', 'モーダル作って', 'カード作って', 'ナビゲーション作って'. Takes natural language descriptions and generates production-ready React + Tailwind + shadcn/ui code with voice progress reports."
version: 1.0.0
---

# UIジェネレータースキル — テキストからUIコードを生成

自然言語の説明文からプロダクション品質の React + Tailwind CSS + shadcn/ui コードを生成するスキル。v0 や Stitch のように、「こんな画面が欲しい」と伝えるだけでコンポーネントコードを出力する。生成の各段階で音声フィードバックを行う。

## CLI パス

```
NOTIFY="${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh"
```

## 生成フロー（4ステップ）

### ステップ1: 要件の理解

```bash
$NOTIFY "UIの要件を確認します。どんな画面を作りますか？"
```

ユーザーの説明から以下を特定する：

- **画面の種類**: ダッシュボード、フォーム、テーブル、LP、設定画面など
- **必要な要素**: ヘッダー、サイドバー、カード、ボタン、入力欄など
- **データ**: 表示するデータの種類と構造
- **インタラクション**: クリック、送信、フィルタリングなどの操作
- **レスポンシブ要件**: モバイル対応が必要か

### ステップ2: 設計判断

```bash
$NOTIFY "設計を決めます。コンポーネント構成を考えています"
```

以下のデザインシステムに基づいて構成を決定する。

### ステップ3: コード生成

```bash
$NOTIFY "コードを生成しています"
```

### ステップ4: 完了報告

```bash
$NOTIFY "UIコードの生成が完了しました。ファイルに書き出しました" complete
```

## デザインシステムトークン

生成するすべてのコードに以下のトークンを適用する。

### スペーシング（4px ベーススケール）
```
Tailwind マッピング:
p-1  = 4px     gap-1  = 4px
p-2  = 8px     gap-2  = 8px
p-4  = 16px    gap-4  = 16px
p-6  = 24px    gap-6  = 24px
p-8  = 32px    gap-8  = 32px
p-12 = 48px    gap-12 = 48px
p-16 = 64px    gap-16 = 64px
```

### カラーパレット
```
背景系:
bg-[#f9f9f9]  ... ページ背景
bg-white      ... カード・コンテナ背景
bg-[#f0f0f0]  ... セカンダリ背景

テキスト系:
text-[#191919]  ... 見出し
text-[#333333]  ... 本文
text-[#666666]  ... サブテキスト
text-[#a0a0a0]  ... プレースホルダー

ボーダー系:
border-[#e0e0e0]  ... デフォルトボーダー

アクセント系:
bg-[#0057ff]      ... プライマリボタン背景
text-[#0057ff]    ... リンク、アクセント
hover:bg-[#0046cc] ... プライマリホバー

セマンティック:
text-green-600 / bg-green-50   ... 成功
text-yellow-600 / bg-yellow-50 ... 警告
text-red-600 / bg-red-50       ... エラー
```

### タイポグラフィ
```
text-3xl font-bold    ... ページタイトル (32px)
text-2xl font-semibold ... セクション見出し (24px)
text-lg font-semibold  ... カード見出し (18px)
text-base              ... 本文 (16px)
text-sm                ... 補足テキスト (14px)
text-xs                ... 注釈 (12px)
```

### レスポンシブブレークポイント
```
デフォルト       ... モバイル（< 640px）
sm:             ... 640px以上
md:             ... 768px以上
lg:             ... 1024px以上
xl:             ... 1280px以上
```

## コンポーネントパターン集

### パターン1: カードグリッド
```tsx
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
  {items.map((item) => (
    <div key={item.id} className="rounded-xl border border-[#e0e0e0] bg-white p-6 shadow-sm hover:shadow-md transition-shadow">
      <h3 className="text-lg font-semibold text-[#191919]">{item.title}</h3>
      <p className="mt-2 text-sm text-[#666666]">{item.description}</p>
      <div className="mt-4 flex items-center justify-between">
        <span className="text-xs text-[#a0a0a0]">{item.date}</span>
        <Button variant="ghost" size="sm">詳細</Button>
      </div>
    </div>
  ))}
</div>
```

### パターン2: フォーム
```tsx
<form onSubmit={handleSubmit} className="mx-auto max-w-lg space-y-6">
  <div className="space-y-2">
    <Label htmlFor="name">名前</Label>
    <Input id="name" placeholder="名前を入力" required />
  </div>
  <div className="space-y-2">
    <Label htmlFor="email">メールアドレス</Label>
    <Input id="email" type="email" placeholder="mail@example.com" required />
  </div>
  <div className="space-y-2">
    <Label htmlFor="message">メッセージ</Label>
    <Textarea id="message" placeholder="内容を入力" rows={4} />
  </div>
  <Button type="submit" className="w-full bg-[#0057ff] hover:bg-[#0046cc]">
    送信
  </Button>
</form>
```

### パターン3: データテーブル
```tsx
<div className="rounded-xl border border-[#e0e0e0] overflow-hidden">
  <Table>
    <TableHeader>
      <TableRow className="bg-[#f0f0f0]">
        <TableHead className="font-semibold text-[#191919]">名前</TableHead>
        <TableHead className="font-semibold text-[#191919]">ステータス</TableHead>
        <TableHead className="font-semibold text-[#191919]">日付</TableHead>
        <TableHead className="text-right font-semibold text-[#191919]">操作</TableHead>
      </TableRow>
    </TableHeader>
    <TableBody>
      {data.map((row) => (
        <TableRow key={row.id} className="hover:bg-[#f9f9f9]">
          <TableCell className="text-[#333333]">{row.name}</TableCell>
          <TableCell><Badge variant={row.statusVariant}>{row.status}</Badge></TableCell>
          <TableCell className="text-[#666666]">{row.date}</TableCell>
          <TableCell className="text-right">
            <Button variant="ghost" size="sm">編集</Button>
          </TableCell>
        </TableRow>
      ))}
    </TableBody>
  </Table>
</div>
```

### パターン4: ナビゲーション
```tsx
<header className="sticky top-0 z-50 border-b border-[#e0e0e0] bg-white/80 backdrop-blur-sm">
  <div className="mx-auto flex h-16 max-w-7xl items-center justify-between px-4 sm:px-6">
    <div className="flex items-center gap-8">
      <span className="text-xl font-bold text-[#191919]">Logo</span>
      <nav className="hidden md:flex items-center gap-6">
        <a href="#" className="text-sm text-[#666666] hover:text-[#191919] transition-colors">機能</a>
        <a href="#" className="text-sm text-[#666666] hover:text-[#191919] transition-colors">料金</a>
        <a href="#" className="text-sm text-[#666666] hover:text-[#191919] transition-colors">ドキュメント</a>
      </nav>
    </div>
    <div className="flex items-center gap-4">
      <Button variant="ghost" size="sm">ログイン</Button>
      <Button size="sm" className="bg-[#0057ff] hover:bg-[#0046cc]">新規登録</Button>
    </div>
  </div>
</header>
```

### パターン5: モーダルダイアログ
```tsx
<Dialog open={open} onOpenChange={setOpen}>
  <DialogContent className="sm:max-w-md">
    <DialogHeader>
      <DialogTitle className="text-[#191919]">確認</DialogTitle>
      <DialogDescription className="text-[#666666]">
        この操作は取り消せません。本当に実行しますか？
      </DialogDescription>
    </DialogHeader>
    <DialogFooter className="gap-2 sm:gap-0">
      <Button variant="ghost" onClick={() => setOpen(false)}>キャンセル</Button>
      <Button className="bg-red-600 hover:bg-red-700" onClick={handleConfirm}>削除する</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

## アクセシビリティ要件

生成するコードには以下を必ず含める：

1. **セマンティック HTML** — `<nav>`, `<main>`, `<section>`, `<article>`, `<aside>`, `<header>`, `<footer>` を適切に使用
2. **ARIA ラベル** — アイコンボタンには `aria-label`、状態には `aria-expanded`, `aria-selected` を付与
3. **キーボードナビゲーション** — Tab 操作ですべてのインタラクティブ要素に到達可能にする。Escape でモーダルを閉じる
4. **フォーカス表示** — `focus-visible:ring-2 focus-visible:ring-[#0057ff]` でフォーカスリングを表示
5. **色のコントラスト** — WCAG AA 基準（4.5:1 以上）を満たすテキストカラー
6. **代替テキスト** — 画像には必ず `alt` 属性を付与

## 生成ルール

1. **モバイルファースト** — デフォルトスタイルはモバイル。ブレークポイントで拡張
2. **shadcn/ui を優先** — Button, Input, Label, Card, Dialog, Table, Badge, Textarea 等は shadcn/ui を使用
3. **TypeScript** — すべてのコンポーネントを `.tsx` で生成。props に型定義を付与
4. **状態管理** — ローカル状態は `useState`。フォームは `react-hook-form` を推奨
5. **命名規則** — コンポーネント名は PascalCase、ファイル名はケバブケースまたはコンポーネント名と一致
6. **1ファイル1コンポーネント** — 再利用可能な単位に分割する

## 音声報告のルール

- 要件確認時に「どんな画面ですか？」と質問
- 設計判断時に「コンポーネント構成」を宣言
- 生成中に「コードを書いています」と報告
- 完了時に「何画面/何コンポーネント生成したか」を complete で報告
- 複数ファイル生成時は進捗を都度報告する
