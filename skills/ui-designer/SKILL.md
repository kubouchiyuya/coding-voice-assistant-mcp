---
name: ui-designer
description: "This skill should be used when the user says '画面設計', 'ワイヤーフレーム', 'UI設計', 'デザイン思考', 'wireframe', 'design system', 'ユーザーフロー', 'IA設計', '情報設計', 'レイアウト', 'モックアップ', 'prototype', 'デザイントークン', 'design tokens'. Provides professional design methodology from purpose analysis through visual design with ASCII wireframes and component specifications."
version: 1.0.0
---

# UIデザイナースキル — デザイン思考で画面を設計する

プロフェッショナルなデザイン手法に基づいてUI/UXを設計するスキル。目的の定義からIA（情報アーキテクチャ）、ワイヤーフレーム、デザイントークン、コンポーネント仕様書までを段階的に作成する。設計の各フェーズで音声による進捗報告を行う。

## CLI パス

```
NOTIFY="${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh"
```

## デザインプロセス（3フェーズ）

### フェーズ1: Meaning — 意味を定義する

```bash
$NOTIFY "まずデザインの目的を整理します。誰のための、何のための画面ですか？"
```

#### 1-1. 目的の明確化

以下の問いに答えてデザインの方向性を定める：

| 問い | 回答例 |
|------|--------|
| このプロダクトの目的は？ | タスク管理を効率化する |
| 主要なユーザーは誰？ | チームリーダー、メンバー |
| ユーザーの主な課題は？ | タスクの優先度が見えない |
| 成功とは何か？ | タスク完了率が20%向上 |
| 競合との差別化は？ | 音声操作による直感的な管理 |

#### 1-2. ユーザーペルソナ（簡易版）

```
ペルソナ: {名称（役割名で記載）}
役割: {職種・立場}
目標: {達成したいこと}
課題: {現在困っていること}
利用場面: {いつ・どこで使うか}
技術レベル: {初心者/中級/上級}
```

```bash
$NOTIFY "ペルソナを定義しました。次に画面の構造を設計します"
```

### フェーズ2: Structure — 構造を設計する

```bash
$NOTIFY "情報アーキテクチャを設計します"
```

#### 2-1. 情報アーキテクチャ（IA）

サイトマップをツリー構造で表現する：

```
[アプリ名]
├── ダッシュボード（ホーム）
│   ├── 概要パネル
│   ├── 最近のアクティビティ
│   └── クイックアクション
├── 機能A
│   ├── 一覧
│   ├── 詳細
│   └── 作成/編集
├── 機能B
│   └── ...
└── 設定
    ├── プロフィール
    ├── 通知設定
    └── アカウント
```

#### 2-2. ユーザーフロー

主要タスクのフローを定義する：

```
[開始] → (条件判定) → [アクション] → [結果]
                    ↓
              [代替パス] → [エラー処理]
```

#### 2-3. 画面遷移図

```
[ログイン] → [ダッシュボード] → [一覧] → [詳細]
                  ↓                        ↓
              [作成]              [編集] → [確認] → [完了]
```

```bash
$NOTIFY "構造設計が完了しました。次にビジュアルデザインに進みます"
```

### フェーズ3: Form — 形を与える

```bash
$NOTIFY "ワイヤーフレームを作成します"
```

#### 3-1. ASCII ワイヤーフレーム

テキストベースでレイアウトを表現する。

**ダッシュボード例:**
```
┌─────────────────────────────────────────────┐
│ [Logo]          Search [________]    [Avatar]│
├──────────┬──────────────────────────────────┤
│          │                                   │
│ Dashboard│  ┌─────────┐ ┌─────────┐         │
│ Tasks    │  │  Stats   │ │  Stats  │         │
│ Projects │  │  Card 1  │ │  Card 2 │         │
│ Team     │  └─────────┘ └─────────┘         │
│ Settings │                                   │
│          │  ┌───────────────────────┐       │
│          │  │   Recent Activity     │       │
│          │  │   - item 1            │       │
│          │  │   - item 2            │       │
│          │  │   - item 3            │       │
│          │  └───────────────────────┘       │
├──────────┴──────────────────────────────────┤
│ Footer                          © 2026      │
└─────────────────────────────────────────────┘
```

**フォーム画面例:**
```
┌─────────────────────────────────────────────┐
│            Create New Task                   │
├─────────────────────────────────────────────┤
│                                              │
│  Title     [________________________]        │
│                                              │
│  Category  [▼ Select category     ]         │
│                                              │
│  Priority  ○ High  ● Medium  ○ Low          │
│                                              │
│  Due Date  [____/____/____] [📅]             │
│                                              │
│  Notes     [________________________]        │
│            [________________________]        │
│            [________________________]        │
│                                              │
│        [Cancel]          [Create Task]       │
│                                              │
└─────────────────────────────────────────────┘
```

#### 3-2. デザイントークン

```
## Spacing（4px ベース）
xs:   4px    (0.25rem)
sm:   8px    (0.5rem)
md:   16px   (1rem)
lg:   24px   (1.5rem)
xl:   32px   (2rem)
2xl:  48px   (3rem)
3xl:  64px   (4rem)

## Colors（ニュートラルパレット + アクセント）
gray-50:   #f9f9f9    (背景)
gray-100:  #f0f0f0    (カード背景)
gray-200:  #e0e0e0    (ボーダー)
gray-400:  #a0a0a0    (プレースホルダー)
gray-600:  #666666    (サブテキスト)
gray-800:  #333333    (本文テキスト)
gray-900:  #191919    (見出し)
accent:    #0057ff    (プライマリアクション)
success:   #00a854    (成功)
warning:   #faad14    (警告)
error:     #f5222d    (エラー)

## Typography
heading-1:  32px / 1.2 / 700   (ページタイトル)
heading-2:  24px / 1.3 / 600   (セクション見出し)
heading-3:  18px / 1.4 / 600   (カード見出し)
body:       16px / 1.6 / 400   (本文)
body-sm:    14px / 1.5 / 400   (補足テキスト)
caption:    12px / 1.4 / 400   (注釈)

## Border Radius
sm:   4px
md:   8px
lg:   12px
full: 9999px  (円形)

## Shadow
sm:   0 1px 2px rgba(0,0,0,0.06)
md:   0 4px 12px rgba(0,0,0,0.08)
lg:   0 8px 24px rgba(0,0,0,0.12)
```

#### 3-3. レスポンシブブレークポイント

```
mobile:    < 640px    (1カラム、ハンバーガーメニュー)
tablet:    640-768px  (2カラム、折りたたみサイドバー)
laptop:    768-1024px (2カラム、固定サイドバー)
desktop:   1024-1280px(3カラム、全要素表示)
wide:      > 1280px   (最大幅制限、中央寄せ)
```

#### 3-4. コンポーネント仕様書

```
## Button コンポーネント
- Variants: primary | secondary | ghost | danger
- Sizes: sm (32px) | md (40px) | lg (48px)
- States: default | hover | active | disabled | loading
- Border radius: md (8px)
- Font: body (16px) / 500
- Padding: sm=8-16px | md=12-24px | lg=16-32px

## Card コンポーネント
- Padding: lg (24px)
- Background: gray-100
- Border: 1px solid gray-200
- Border radius: lg (12px)
- Shadow: sm (default) → md (hover)
- Header: heading-3 + optional action
- Body: body text
- Footer: optional action buttons

## Input コンポーネント
- Height: md (40px) | lg (48px)
- Border: 1px solid gray-200 → accent (focus)
- Border radius: md (8px)
- Padding: 12px horizontal
- Label: body-sm / 500, margin-bottom sm
- Error: error color, caption size
- States: default | focus | error | disabled
```

## デザイン原則

1. **グリッドファースト** — 4px ベースグリッドにすべてを揃える
2. **幾何学的ミニマリズム** — 装飾を最小限に。余白で語る
3. **ニュートラルパレット + アクセント** — グレースケール基調に青のアクセント
4. **タイポグラフィ階層** — 3段階の見出し + 2段階の本文で情報に序列をつける
5. **一貫性** — 同じパターンは同じ見た目にする。例外を作らない

## 出力フォーマット

このスキルの最終成果物は以下の順序で提供する：

1. **ペルソナ定義**（テキスト）
2. **情報アーキテクチャ**（ツリー図）
3. **ユーザーフロー**（フロー図）
4. **ASCII ワイヤーフレーム**（各画面）
5. **デザイントークン**（変数定義）
6. **コンポーネント仕様**（各コンポーネント）

```bash
$NOTIFY "UI設計が完了しました。ワイヤーフレームとデザイントークンを出力しています" complete
```

## 音声報告のルール

- 各フェーズの**開始時**に何をするか宣言
- フェーズ**完了時**に成果を要約
- ユーザーの判断が必要な場面では confirm で確認
- 最終成果物の出力完了時に complete で報告
