---
name: performance-tune
description: "This skill should be used when the user says '速くして', '最適化', 'パフォーマンス', 'optimize', '遅い', 'slow', 'heavy', '重い', 'バンドルサイズ', 'bundle size', 'Lighthouse', 'Core Web Vitals', 'LCP', 'CLS', 'FID', 'メモリリーク', 'memory leak', 'レンダリング', '描画が遅い'. Performs bundle analysis, image optimization, code splitting, caching strategy, and React rendering optimization with Lighthouse audit integration."
version: 1.0.0
---

# パフォーマンスチューニングスキル — 計測・分析・最適化

Webアプリケーションのパフォーマンスを計測し、ボトルネックを特定して最適化するスキル。バンドル分析、画像最適化、コード分割、キャッシング、レンダリング最適化を網羅する。Lighthouse との統合で Core Web Vitals を改善する。各ステップで音声による進捗報告を行う。

## CLI パス

```
NOTIFY="${CLAUDE_PLUGIN_ROOT}/scripts/notify.sh"
```

## 最適化フロー（5ステップ）

### ステップ1: 現状計測

```bash
$NOTIFY "パフォーマンスの現状を計測します"
```

#### Lighthouse CLI による計測
```bash
# Lighthouse のインストール（未インストールの場合）
npm i -g lighthouse

# 計測実行
lighthouse http://localhost:3000 --output=json --output-path=./lighthouse-report.json --chrome-flags="--headless"

# スコアの確認
cat lighthouse-report.json | grep -A1 '"performance"'
```

#### Core Web Vitals の基準値
| 指標 | 良好 | 改善が必要 | 不良 |
|------|------|------------|------|
| LCP（Largest Contentful Paint） | < 2.5s | 2.5-4.0s | > 4.0s |
| FID（First Input Delay） | < 100ms | 100-300ms | > 300ms |
| CLS（Cumulative Layout Shift） | < 0.1 | 0.1-0.25 | > 0.25 |
| INP（Interaction to Next Paint） | < 200ms | 200-500ms | > 500ms |
| TTFB（Time to First Byte） | < 800ms | 800-1800ms | > 1800ms |

```bash
$NOTIFY "計測が完了しました。パフォーマンススコアは{N}点です"
```

### ステップ2: バンドル分析

```bash
$NOTIFY "バンドルサイズを分析します"
```

#### webpack の場合
```bash
# バンドルアナライザーのインストール
npm i -D webpack-bundle-analyzer

# 分析レポートの生成
npx webpack --profile --json > stats.json
npx webpack-bundle-analyzer stats.json
```

#### Vite の場合
```bash
# rollup-plugin-visualizer の使用
npm i -D rollup-plugin-visualizer

# vite.config.ts に追加して build
npm run build
```

#### Next.js の場合
```bash
# Next.js 組み込みアナライザー
npm i -D @next/bundle-analyzer

# 分析付きビルド
ANALYZE=true npm run build
```

#### バンドルサイズの目安
| 対象 | 推奨サイズ | 警告ライン |
|------|------------|------------|
| 初期JS（gzip） | < 100KB | > 200KB |
| 初期CSS（gzip） | < 50KB | > 100KB |
| 個別チャンク | < 50KB | > 100KB |
| 画像（1枚あたり） | < 200KB | > 500KB |
| 合計ページ重量 | < 1MB | > 3MB |

```bash
$NOTIFY "バンドル分析が完了しました。初期JSは{N}KBです"
```

### ステップ3: 最適化の実施

```bash
$NOTIFY "最適化を実施します。順番に対処していきます"
```

#### 3-1. コード分割（Code Splitting）

**React.lazy による遅延読み込み:**
```tsx
import { lazy, Suspense } from 'react';

// ルートベースの分割
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));

// 使用時
<Suspense fallback={<div className="animate-pulse h-screen bg-gray-100" />}>
  <Dashboard />
</Suspense>
```

**動的 import によるライブラリの遅延読み込み:**
```tsx
// 重いライブラリは使用時に読み込む
const handleExport = async () => {
  const { default: jsPDF } = await import('jspdf');
  const doc = new jsPDF();
  // ...
};
```

**Next.js の dynamic import:**
```tsx
import dynamic from 'next/dynamic';

const HeavyChart = dynamic(() => import('../components/Chart'), {
  loading: () => <div className="animate-pulse h-64 bg-gray-100 rounded-xl" />,
  ssr: false,
});
```

#### 3-2. 画像最適化

**Next.js Image:**
```tsx
import Image from 'next/image';

<Image
  src="/hero.jpg"
  alt="ヒーロー画像"
  width={1200}
  height={630}
  priority          // LCP 画像は priority を指定
  placeholder="blur"
  blurDataURL={blurUrl}
/>
```

**一般的な最適化手法:**
```bash
# WebP 変換（cwebp）
cwebp -q 80 input.png -o output.webp

# AVIF 変換
npx avif --input="./images/*.png" --quality=50

# sharp による一括最適化
npm i sharp
```

**画像フォーマットの選択基準:**
| 用途 | フォーマット | 理由 |
|------|-------------|------|
| 写真 | WebP / AVIF | 高圧縮率 |
| アイコン・ロゴ | SVG | スケーラブル |
| アニメーション | WebP / Lottie | GIFより軽量 |
| OGP画像 | JPEG（フォールバック） | 互換性 |

#### 3-3. レンダリング最適化（React）

**React.memo — 不要な再レンダリングの防止:**
```tsx
const ExpensiveList = React.memo(({ items }: { items: Item[] }) => {
  return (
    <ul>
      {items.map(item => <li key={item.id}>{item.name}</li>)}
    </ul>
  );
});
```

**useMemo — 高コストな計算のメモ化:**
```tsx
const sortedItems = useMemo(() => {
  return items.slice().sort((a, b) => b.score - a.score);
}, [items]);
```

**useCallback — コールバック関数のメモ化:**
```tsx
const handleClick = useCallback((id: string) => {
  setSelected(id);
}, []);
```

**仮想化（大量リスト）:**
```tsx
import { useVirtualizer } from '@tanstack/react-virtual';

// 1000件以上のリストは仮想化する
const virtualizer = useVirtualizer({
  count: items.length,
  getScrollElement: () => parentRef.current,
  estimateSize: () => 48,
});
```

#### 3-4. キャッシング戦略

**HTTP キャッシュヘッダー:**
```
# 静的アセット（JS/CSS/画像）— 1年キャッシュ + immutable
Cache-Control: public, max-age=31536000, immutable

# HTMLページ — 常に再検証
Cache-Control: no-cache

# API レスポンス — 短期キャッシュ
Cache-Control: private, max-age=60, stale-while-revalidate=300
```

**SWR / React Query によるデータキャッシュ:**
```tsx
import useSWR from 'swr';

const { data, error, isLoading } = useSWR('/api/users', fetcher, {
  revalidateOnFocus: false,
  dedupingInterval: 60000,  // 60秒間は重複リクエストを防止
});
```

**Service Worker によるオフラインキャッシュ:**
```bash
# next-pwa の導入（Next.js）
npm i next-pwa
```

#### 3-5. フォント最適化

```tsx
// Next.js のフォント最適化
import { Inter } from 'next/font/google';

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',         // FOIT を防止
  preload: true,
});
```

**フォント最適化のルール:**
- `font-display: swap` を必ず指定
- サブセット化で日本語フォントを軽量化
- プリロードで LCP を改善
- 使用するウェイトのみ読み込む

### ステップ4: 最適化後の再計測

```bash
$NOTIFY "最適化が完了しました。再計測します"
```

```bash
# 再ビルドとサイズ確認
npm run build

# Lighthouse 再計測
lighthouse http://localhost:3000 --output=json --output-path=./lighthouse-after.json --chrome-flags="--headless"
```

### ステップ5: 結果報告

```bash
$NOTIFY "最適化の結果をお伝えします"
```

レポートフォーマット：
```
## パフォーマンス最適化レポート

### スコア比較
| 指標 | 最適化前 | 最適化後 | 改善幅 |
|------|----------|----------|--------|
| Performance | {N} | {N} | +{N} |
| LCP | {N}s | {N}s | -{N}s |
| CLS | {N} | {N} | -{N} |
| バンドルサイズ | {N}KB | {N}KB | -{N}KB |

### 実施した最適化
1. {最適化内容1}
2. {最適化内容2}
3. ...

### さらなる改善提案
- {追加提案1}
- {追加提案2}
```

```bash
$NOTIFY "パフォーマンススコアが{N}点から{N}点に改善しました" complete
```

## 最適化の優先順位

問題が複数ある場合は以下の順に対処する：

1. **LCP の改善** — ユーザー体感への影響が最大
2. **バンドルサイズの削減** — 初期読み込み速度の改善
3. **画像最適化** — 簡単に大きな効果が得られる
4. **コード分割** — 初期ロードの軽量化
5. **レンダリング最適化** — インタラクション速度の改善
6. **キャッシング** — リピート訪問の高速化
7. **フォント最適化** — CLS と LCP の微調整

## 音声報告のルール

- 最適化**開始時**に「パフォーマンスを計測します」と宣言
- 計測結果を**スコアで**音声報告（「スコアは72点です」）
- 重大な問題は**即座に**音声で警告（「バンドルサイズが500KBを超えています」）
- 各最適化ステップの**完了ごと**に効果を報告
- 全体の**最終結果**を before/after で complete 報告
