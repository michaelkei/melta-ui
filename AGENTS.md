# melta UI - プロジェクト共通ガイド

> このファイルは Claude Code / Cursor / Copilot / Codex 等のAIコード生成ツール共通の指示書。
> ツール固有の設定は各ツールの設定ファイル（CLAUDE.md 等）を参照。

---

## プロジェクト概要

melta UI は AI コード生成に最適化されたデザインシステム（DS）。
Tailwind CSS ベースのセマンティックトークンと禁止パターンにより、AIが生成するUIの品質下限を保証する。

- **対象**: B2B / B2C 両方の汎用デザインシステム
- **エンドユーザー**: 業務SaaS、EC、予約、学習、医療、行政など幅広いドメイン
- **DSの消費者**: 人間の開発者・デザイナー、および AI コード生成エージェント

---

## 技術スタック

| 項目 | 値 |
|------|-----|
| CSS フレームワーク | Tailwind CSS（CDN） |
| フォント | Inter, Hiragino Sans, Hiragino Kaku Gothic ProN, Noto Sans JP |
| アイコン | Charcoal（207個）+ Lucide（15個補完） |
| トークン | `tokens/tokens.json`（106トークン） |
| メタデータ | `metadata/components.json`（28コンポーネント） |
| ホスティング | Netlify（手動デプロイ） |
| 本番URL | https://melta.tsubotax.com |

---

## デプロイ

```bash
# 本番デプロイ（--dir 指定不要。netlify.toml の publish = "." が使われる）
netlify deploy --prod
```

> `netlify deploy --prod --dir=docs` は NG。`publish = "."` なのでルートからデプロイする。

---

## 設計原則（5つ）

1. **Layered** -- Background → Surface → Text/Object の3層でUIを構成する
2. **Contrast** -- テキストは背景に対してWCAG 2.1準拠（4.5:1以上）
3. **Semantic** -- 色は用途で指定する（`bg-surface-primary` ≠ 生の `bg-white`）
4. **Minimal** -- 1つのViewに使う色は3色まで（背景・アクセント・テキスト）
5. **Grid** -- スペーシングは4の倍数を基本、8の倍数を推奨する

---

## Design Context

### Brand Personality
- **3語で表すと**: 静謐・精緻・温もり（Quiet / Precise / Warm）
- **声のトーン**: 「声を張らずに伝わる」-- 主張しすぎない、でも確かに伝わる
- **感情目標**: 心地よい集中 → 洗練された効率 → 穏やかな親しみ（この順で優先）

### Aesthetic Direction
- **ビジュアルトーン**: ミニマルだが冷たくない。フラットな基盤に3層で奥行きを出す
- **参考**: Linear / Notion / Stripe / Vercel
- **アンチリファレンス**: 派手なグラデーション・ネオンカラー、Bootstrap的テンプレート

### Design Principles
1. **Content First** -- UIは黒子。コンテンツが主役
2. **Calm Confidence** -- 正確なスペーシングとコントラストで品質を示す
3. **Inclusive by Default** -- WCAG 2.1 AA準拠はデフォルト
4. **Systematic Warmth** -- 一貫性を保ちつつ人間味を添える
5. **Machine-Readable** -- トークン・メタデータによりAIが正確にUIを生成できる

---

## HTMLテンプレート

Tailwind CDN 使用時は `<head>` に必ず含める。`primary-*` が未定義だとセマンティックカラーが機能しない。

```html
<script src="https://cdn.tailwindcss.com"></script>
<script>
tailwind.config = {
  theme: {
    extend: {
      colors: {
        primary: {
          50:'#f0f5ff',100:'#dde8ff',200:'#c0d4ff',300:'#95b6ff',
          400:'#6492ff',500:'#2b70ef',600:'#2250df',700:'#1a40b5',
          800:'#13318d',900:'#0e266a',950:'#07194e'
        },
        wf: { bg:'#FFFFFF', surface:'#F5F5F5', border:'#E0E0E0', text:'#333333', 'text-sub':'#888888', accent:'#666666' }
      },
      fontFamily: {
        sans: ['Inter','Hiragino Sans','Hiragino Kaku Gothic ProN','Noto Sans JP','sans-serif']
      }
    }
  }
}
</script>
<style>.text-body { color: #3d4b5f; }</style>
```

---

## 禁止パターン要約

| 禁止 | 代替 |
|------|------|
| `text-black` | `text-slate-900` |
| `bg-gray-300`以上の背景 | `bg-gray-50` 〜 `bg-gray-200` |
| `rounded-none` on cards | `rounded-xl` |
| `shadow-lg` / `shadow-2xl` | `shadow-sm` 〜 `shadow-md`（オーバーレイ: `shadow-xl`） |
| `border-gray-100` | `border-slate-200` |
| `text-gray-400` for body | `text-body` (#3d4b5f) |
| `py-0.5` for buttons | `h-8` 以上（S: `h-8` / M: `h-10` / L: `h-12`） |
| カード/Alert上部・左端のカラーバー | `border border-*-200 rounded-lg` で全周ボーダー |
| カード直下の `<fieldset>` + `<legend>` | `<div>` + `<h2>` でセクション見出し |
| 色だけで情報伝達 | アイコン/テキストを併用 |
| `tracking-tight` | 見出し1%、本文2%を基本 |
| プレースホルダーのみのラベル | 必ず `<label>` を使用 |
| 派手なグラデーション / ネオンカラー / 過剰なアニメーション | セマンティックカラー、150〜300ms に限定 |
| `bg-indigo-*` / `bg-blue-*` 等のハードコード | `primary-*` を使用 |
| `<th>` の `scope` 省略 | `<th scope="col">` 必須 |
| `<nav>` の `aria-label` 省略 | `aria-label="メインナビゲーション"` 必須 |
| 日付セレクトの均等幅（`grid-cols-3`） | `flex` + 年 `w-28`、月・日 `w-20` |
| フォーム制御ラベル包含divの `leading-normal` 省略 | 包含 `<div>` に `leading-normal` 付与 |

> 全禁止パターン（76項目）: `foundations/prohibited.md` 参照

---

## ディレクトリ構成

| ディレクトリ | 内容 |
|---|---|
| `foundations/` | color, spacing, typography, elevation, radius, motion, z-index, icons, accessibility, emotional-feedback（10ファイル） |
| `components/` | button, card, checkbox, modal, sidebar 等（28コンポーネント） |
| `patterns/` | layout, form, navigation, interaction-states, responsive（5ファイル） |
| `tokens/` | `tokens.json`（機械可読トークン） |
| `metadata/` | `components.json`（コンポーネントメタデータ） |
| `skills/` | design-review（DSチェック・違反検出） |
| `assets/icons/` | Charcoal SVG（207個）+ Lucide SVG（15個） |

---

## テーマ・ダークモード

> テーマ設定・CSS変数定義・ダークモード切替: `foundations/theme.md` を参照。

| 設定 | 値 |
|------|-----|
| **ダークモード** | `OFF` |

- `OFF`: ライトモードのみで設計・生成する（デフォルト）
- `ON`: ダークモード対応を含める（`foundations/theme.md` + `foundations/color.md` Dark列 を参照）
