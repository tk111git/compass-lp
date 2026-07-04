# HANDOFF — compass-lp（AIxSA Compass LP・2スロット目）

| 項目 | 内容 |
|---|---|
| slot | `aixsa-compass-lp`（slots.json 既定） |
| subdomain | `compass.info-shared.com` |
| Worker | `compass-lp` |
| plane / tier | static / B0（reach.public・認証ゼロ） |
| 経路 | CF-RUNBOOK §2【A】 |

## 0. Human 決定待ち（コード外・3値のみ）

deploy 前に dist/index.html の3箇所を実値に置換する:

| # | 現在値 | 置換対象 | 箇所 |
|---|---|---|---|
| T1 | `https://forms.gle/your-form-id` | 実 Google フォーム URL（要作成） | L1165 |
| T2 | `contact@example.com` | 実メールアドレス | L1169, L1176（mailto 2箇所） |
| T3 | `© 2026 AIxSA Compass.` | 正式な事業者名表記（現状で可なら不変更） | footer |

※ T1/T2 未確定でも repo 作成〜Workers Builds 接続までは先行可能。
   Custom Domain 結線（公開）だけを T1/T2 確定後にする、でも良い。

## 1. 手順（決定論・機械実行可）

```bash
# repo 初期化 & push（tk111git 配下・公開 repo・秘密ゼロ）
cd compass-lp
git init -b main && git add -A && git commit -m "compass-lp: initial (LP v3)"
gh repo create tk111git/compass-lp --public --source=. --push
```

## 2. dashboard UI 操作（API 不可・人間 2操作のみ）

```
① Workers Builds 接続:
   Workers & Pages → Create application → Import a repository → tk111git/compass-lp
   Production branch: main / Deploy command: npx wrangler deploy / Root: /
② Custom Domain:
   Worker compass-lp → Settings → Domains & Routes → Add Custom Domain
   → compass.info-shared.com（proxied 自動生成・手動 CNAME 禁止 / M4）
```

## 3. 検証（完了条件）

```bash
curl -sI https://compass.info-shared.com | head -3   # HTTP/2 200
# workers.dev 側が 404/error であること（露出 = custom domain のみ / M1）
```

## 4. 完了後の記帳

- CF-STATUS §7 に1行追加: `compass.info-shared.com | 静的 | compass-lp | proxied live`
- slots.json は据え置き（R-d2）。ただし live が2枚になった時点で A-1（Phase A 完了宣言 + バッチ同期）の trigger 充足を Taka に上申。

## メモ（設計判断の由来）

- v3 を採用（`aixsa_lp.html` は旧世代・アーカイブ）。docx は slot 化せず据え置き。
- 5月メモの WordPress / aixsa.jp 前提は substrate 決定（CF Worker）で置換済み。
- LP は B0 資産: CTA = mailto/フォームのみ・checkout なし ∴ 認証/課金 infra ゼロで公開可。
  Compass 製品本体（B2・subscription）の機構はこの deploy を gate しない。
