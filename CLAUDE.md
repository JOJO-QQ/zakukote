# FIXD — 固定費管理 × 教育費シミュレーション

## プロジェクト概要

スマホ向けWebアプリのインタラクティブモック。固定費の無駄を可視化しながら、子供の教育費積立計画を立てるフリーミアムアプリ。

- **公開URL:** https://jojo-qq.github.io/COSTKOTEIHI/
- **ソースファイル:** `index.html`（HTML/CSS/JS 単一ファイル）
- **フレームサイズ:** 390×844px（iPhoneサイズ固定）

---

## ファイル構成

```
index.html   ← すべてここ。HTML + CSS + JS が1ファイルに収まる
CLAUDE.md    ← このファイル
```

---

## アプリの画面構成

| タブ | ID | 役割 |
|-----|-----|-----|
| ホーム | `s-home` | 固定費一覧・達成カード・教育費シミュ・ロードマップ |
| 入力 | `s-input` | 住宅費・通信費・車・月額サービスの選択 |
| 診断 | `s-diag` | 節約診断・解約ガイド |
| 教育 | `s-edu` | 教育費衝撃画面・パス選択・月額積立目安 |

ナビゲーション: `goTab('home')` などで切り替え。

---

## フリーミアム設計

```javascript
let isPro = false;  // true にするとProモード（開発テスト用: activatePro()）
```

- **ロック時:** 子供追加がヒーローカード・whatif/PDF/リマインダーが3列グリッド
- **アンロック時:** 子供追加がフル幅グリーンボタン
- **Proモーダル:** `showProModal('multi'|'whatif'|'pdf'|'remind')`

---

## 主要な状態変数

```javascript
let selected = [];          // 選択中の固定費サービスリスト
let childAge = null;        // 子供の年齢（null=未入力）
let monthlyGoal = 0;        // 月額積立目標
let assetAmount = 0;        // 現在の貯金（Proのみ）
let savedFromDiag = 0;      // 診断で解約した月額合計
let extraChildren = [];     // 2人目以降の子供情報
let isPro = false;
```

---

## 教育費ロードマップの仕組み

```javascript
buildRoadmapHtml(age, goal, startBal = 0)
// → { html, bal }
// 結果カード（大学卒業後残高）が最上部に来る
// ステージ: 入学前 → 小学校 → 中学校 → 高校 → 大学
// 計算: 複利なし。monthlyGoal × months の単純積み上げ（アプリポリシー）
```

---

## SVGアイコン体系

絵文字は使わない。すべてインラインSVG（`SVC_ICONS` オブジェクト）。

```javascript
getSvcIco(name, color, fallback)  // name が SVC_ICONS に存在しない場合はフォールバック
```

| キー | 用途 |
|-----|-----|
| `stage-pre/elem/mid/high/univ` | 各ステージアイコン |
| `pro-child/pdf/remind/whatif/lock` | Pro機能アイコン |
| `nav-home/diag/edu` | ナビゲーション |
| `住宅費/通信費/車維持費/月額サービス` | カテゴリ |
| Netflix/Spotify 等 | 各サービスロゴ |

`sHead(icon, name, badge, badgeStyle)` — SVC_ICONSキーなら26px丸角SVG、なければテキスト。

---

## 変えてはいけないこと

1. **複利計算なし** — `monthlyGoal × months` の単純積み上げのみ
2. **単一ファイル** — HTMLを分割しない
3. **Chart.js 4.4.0** — CDN固定、バージョン変更しない
4. **`<details>/<summary>`方式** — ステージ開閉はJSなしのネイティブ要素で

---

## よく使う関数

```javascript
renderHome()         // ホーム画面を再描画
renderGoalCard()     // シミュレーションカードを再描画
renderRoadmap()      // ロードマップを再描画
renderCompareChart() // 比較チャートを再描画
showToast(msg)       // トースト通知（2.5秒）
showProModal(key)    // Proアップグレードモーダルを開く
activatePro()        // isPro=true にしてホーム再描画（テスト用）
markCancelled(name, price) // 診断で解約済みにする
```

---

## 更新・デプロイ手順

```bash
# 1. index.html を編集後
git add index.html
git commit -m "変更内容を一言で"
git push
# → 1〜2分で https://jojo-qq.github.io/COSTKOTEIHI/ に反映
```
