# OVERDUE — Zero Trust Debt Ledger

**English summary:** OVERDUE is a single-file HTML tool that reframes unfinished zero-trust work as **debt on a balance sheet**. Instead of presenting security gaps as a risk score, it books the AS-IS annual expected loss as a liability, credits what implementation has actually repaid, and reports the outstanding balance — split into what you *can* repay, what you *could* repay but organizational friction has stalled, and an irreducible floor that no implementation removes. Every number traces back to two accounting identities, and the tool is deliberately built so that counting things (visibility, aging, threat-speed data) can never inflate the balance. Drivers a team admits it cannot yet count are disclosed as **off-balance** items rather than silently treated as zero, and post-quantum exposure is reported as two non-monetary lenses — harvest-now-decrypt-later time margin (Mosca's X+Y>Z) and the share of encrypted egress that goes uninspected. It ships a printable dunning notice, a full provenance chapter listing every constant and its source, and imports from the companion Forg and MAAZ tools. Runs entirely in the browser — no data leaves the machine (fonts are loaded from Google Fonts). The UI is in Japanese.

---

## 概要

OVERDUE（オーバーデュー）は、**やり残したゼロトラストを「未納の借金」として記帳する**単一 HTML の台帳ツールです。

セキュリティの未対応領域は、通常「リスクスコア」や「成熟度レベル」で語られます。しかしそれは経営の言語ではありません。OVERDUE は同じ実態を会計のメタファーに置き換えます — 残高照会があり、内訳台帳があり、返済があり、督促状が届く。

### 中核の恒等式

```
① ZT負債残高 = AS-IS 期待損失 − 返済（net）
② 残高       = 返済可能残高 + STALLED + 不可避床
```

新しいリスク金額を発明しないこと（恒等式①）と、残高を必ず 3 つに分解できること（恒等式②）が、このツールの設計上の芯です。

| 残高の内訳 | 意味 |
|---|---|
| **返済可能残高** | 技術的にも組織的にも、次に着手できる分 |
| **STALLED** | 技術的には返せるのに、組織摩擦で 1 円も動いていない分。摩擦係数 F = F_org /(1 + F_org) で按分 |
| **不可避床** | 完全実装でも残る確率的残存（baseRisk × climateFloor 項）。返済対象外 |

侵害確率は `baseRisk × climateFloor + riskMax × climateSlope × 脆弱性係数` で表され、脆弱性係数は CISA ZTMM の成熟度と MITRE ATT&CK / ATLAS のカバレッジから逆算されます（担当者の主観では決めない）。baseRisk 項が構造的に残るため、**「導入すれば安全」という約束はこのツールでは書けません**。

### 6 つの章

| 章 | 内容 |
|---|---|
| **00 Balance · 残高照会** | 通帳型の未納残高、勘定元帳（借方 / 貸方 / 残高）、STALLED の指摘 |
| **01 Ledger · 内訳台帳** | 負債の 4 分類 — 未検証の東西通信経路（UNKNOWN / VISIBLE / ENFORCED の状態機械）、期限なき例外アクセス、所有者不明資産への広域権限、根拠未記録の継続権限。さらに新規借入勘定（クラウド・SaaS 拡張、AI エージェント、M&A・拠点追加、PQC 未棚卸し）を件数で記帳し、各ドライバーの取得方法を **実測 / 概算 / 棚卸不能** で自己申告。「棚卸不能」はゼロと区別して**簿外債務**として開示。数え方が分からない欄には回収先マップ（どの台帳・どのツールから、どれくらいの工数で数えるか）を併記 |
| **02 Grace · 猶予時間** | 元本は急に膨らまない。減っているのは猶予のほう — 悪用までの時間（TTE）の推移と、パッチ適用中央値・KEV 修復率といった防御側の指標を対比。**02.4 Quantum Grace** では PQC を件数ではなく 2 レンズ（HNDL = Mosca の X+Y>Z による時間の余白／検査不能な暗号化 egress の比率）で開示 |
| **03 Notice · 督促状** | 経営層に渡せる督促状を別ウィンドウに発行し、印刷 / PDF 保存 |
| **04 Intake · 計上・入力** | MAAZ の JSON（成熟度 → 脆弱性係数）、Forg の JSON（組織摩擦）、ヒアリングシート（.xlsx）を取り込んで自社データで再計算。台帳自体の JSON 保存 / 読み込み、架空モデルケースの読み込み |
| **05 Provenance · 根拠開示** | 恒等式の検算、全定数とその出典（基準日つき）、脅威環境係数のプリセットと上限キャップ、不変条件の一覧、「未検証の暗黙の信頼」を 4 つの勘定に分担する対応表、系譜と免責 |

### 不変条件 — このツールが構造的にできないこと

金額を動かせる経路を意図的に狭めてあります。

- **INV-D3** 可視化（UNKNOWN → VISIBLE）は残高を 1 円も動かさない。件数系の入力は金額計算に配線されていない
- **INV-D4** 金額の唯一の真実は恒等式。新しいリスク金額を発明しない
- **INV-D6** 項目別按分の総和 = 集約残高（二重計上なし）
- **INV-D7** 滞留日数（エージング）は残高を変えない — **利息は採用しない**
- **INV-N1** 新規借入ドライバーは物量（件数 / 期）のみを記帳する。金額が増える唯一の経路は次回棚卸しでの再計上（実測）であり、将来外挿による積み増しはしない
- **INV-N2** 「棚卸不能」と申告されたドライバーはゼロと区別し、簿外債務として開示する。金額化はしない（スコープ外と判明している分は MAAZ の SCF 経由で計上額側に反映済みのため、簿外でも金額化すると二重計上になる）。簿外がある間、残高と新規借入合計は**下限値**として表示される
- **INV-N3** PQC リスクは件数で表さない。HNDL は時間、インフラ検査可能性は比率として 02.4 で開示し、金額残高・侵害確率式には一切接続しない
- **猶予不算入** 「02 猶予時間」の攻撃速度データは残高に一切入らない。脅威環境が金額に触れる唯一の接続点は上限キャップつきの climate 係数のみ

### 出典

定数と時系列は公開レポートに基づき、章 05 に基準日つきで開示されます（Verizon DBIR、IBM Cost of a Data Breach、Mandiant M-Trends、Palo Alto Networks Unit 42、Sophos State of Ransomware、CISA KEV を集計する Zero Day Clock ほか）。PQC の CRQC 想定年（既定 2029）は断定ではなく保守側の想定で、NIST IR 8547 の 2030 年以降非推奨方針と資源見積もりの縮小を根拠に章 02.4 で開示し、利用者が変更できます。**デフォルト値は架空のモデル企業**であり、初期表示にはその旨の警告が出ます。実データを取り込むまでは自社の帳簿ではありません。

### データの扱い

すべての計算とファイル読み取りはブラウザ内で完結し、入力内容が外部に送信されることはありません。外部スクリプトへの依存もありません（フォントのみ Google Fonts）。.xlsx の解析も外部ライブラリを使わず標準 API のみで行っています。

## 使い方

1. `overdue_v1_8_0.html`（または `index.html`）をダウンロードする
2. ブラウザでファイルを開く
3. 「04 計上・入力」から自社データを入力または取り込む（何も取り込まない場合は架空モデル企業の帳簿が表示されます）

### データの入れ方

「04 計上・入力」には 4 つの導線があります。

| 方法 | 説明 |
|---|---|
| **フォーム直接入力** | 基本情報・被害額前提・TO-BE縮小前提・確率係数をその場で入力。追加ファイル不要の主導線です |
| **MAAZ の JSON** | 姉妹ツール MAAZ のエクスポートを取り込むと、成熟度から脆弱性係数が実データになります。v1.8.0 以降は MAAZ の **AS-IS / TO-BE 別スコープ SCF**（`scf` / `scfToBe`）に対応し、TO-BE 側の縮小率は TO-BE スコープの網羅度で減衰します。旧形式の export（`scfToBe` なし）は AS-IS 値へフォールバックし、その旨が画面に表示されます |
| **Forg の JSON** | 姉妹ツール Forg のエクスポートを取り込むと、組織摩擦係数が実データになり STALLED が按分されます |
| **保存済み JSON** | 本ツールでエクスポートした台帳を読み戻して続きから作業 |

> **ヒアリングシート (.xlsx) について**: `_endeavor_import` シートを持つ所定フォーマットの Excel を直接読み込む機能がありますが、**そのテンプレート自体はこのリポジトリには同梱していません**。テンプレートが手元にない場合は、上記のフォーム直接入力または JSON 取り込みをご利用ください。機能上の制約はありません。

ビルド不要・サーバー不要です。GitHub Pages を有効にした場合は `index.html` がそのまま表示されます。

### 動作環境

Chrome / Edge / Firefox / Safari の最新版。JavaScript を有効にしてください。督促状の発行と PDF 保存はブラウザの印刷機能を使用します（ポップアップを許可してください）。

## 関連ツール

- **Forg** — 組織摩擦係数 F_org を算出。その JSON を取り込むと STALLED の按分が実データになります
- **MAAZ** — CISA ZTMM 成熟度と脅威カバレッジを評価。その JSON を取り込むと脆弱性係数が実データになります

いずれも未連携でも動作しますが、Forg 未連携時は摩擦 0 の楽観値（STALLED = 0）で表示される点に注意してください。

## バージョン

- アプリケーション: **v1.8.0**（HTML 内の `const APP_VER` が唯一の版数の出所）
- 計算エンジン: 同系上位エンジン v4.4.1 のフォーク（`const ENGINE_VER = 'v4.4.1 engine (fork · SCF二重化差分あり)'`）。恒等式・定数・出典は同一です。v1.8.0 で MAAZ の SCF 二重化に対応したため、**AS-IS / TO-BE 別スコープの SCF を含む新形式の MAAZ データを取り込んだ場合のみ、TO-BE 側の残高が上流エンジン v4.4.1 と意図的に乖離します**（TO-BE スコープ補正の反映）。旧形式の入力では従来どおり同一の残高を返します
- 保存 JSON の識別子: `format: "overdue-v1"` / `engine: "ztd-v4.4.1-fork"`。読み込み側は旧識別子（`endeavor-*` 形式、旧キー `investZscaler`）も受理する後方互換を持ちます
- 設計システム: Ztelier Edition

エクスポートする JSON のスキーマ版数はアプリ版数とは別軸で管理されています。

### 変更履歴

| 版 | 主な変更 |
|---|---|
| **v1.8.0** | MAAZ の **SCF 二重化（AS-IS / TO-BE 別スコープ）** に追随。TO-BE の縮小率は `scfToBe` で減衰し、欠落時は AS-IS 値へフォールバックして画面に明示。取込時に TO-BE 側 SCF の範囲検証を追加。表示金額・構成比に**最大剰余法（LRM）**を導入し、「合計 = 内訳の和」が表示上も文字どおり成立するよう整合（表示層専用。生値 SSOT・計算エンジン・エクスポートは不変）。`--ink-3` のコントラスト是正、`sub`/`sup` の最小 11px 下限などの表示修正 |
| **v1.7.0** | 簿外債務（棚卸不能ドライバー）の開示、Quantum Grace（PQC の 2 レンズ開示）、狭幅表示・印刷まわりの修正 |
| **v1.1.1** | 初回公開 |

## ライセンス

Apache License 2.0 — 詳細は [LICENSE](./LICENSE) を参照してください。

```
Copyright 2026 takainthecloud-glitch

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0
```

## 免責

本ツールは投資判断の議論を支援する情報提供を目的としています。算出される残高・確率・按分は入力値と公開ベンチマークに基づく目安であり、実際の損害額や防御効果を保証するものではありません。定数は自社のインシデント実績や最新の業界レポートで上書きして使用してください。会計・監査上の負債計上を意図したものではありません。
