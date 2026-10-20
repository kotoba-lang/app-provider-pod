# app-provider-pod（面宣言 x 設定）— itonami（dapp）上の分散型設計

状態: 第 1 スライス実装（2026-10-20）。正本はこの文書。
追跡: Linear JUN-128（親 JUN-116）。

## 位置づけ

app-provider-pod は **Provider Pod Registry & Health Management**（BPMN 駆動の
provider ライフサイクル + pod 編成 + health トラッキング）。本スライスの対象
は appview 面の**自己宣言**（default-db）。この port は scaffold の stale 値を
**wrangler.jsonc の実値で訂正済み** —— 宣言と実設定の一致が判定の対象。

## このスライス: 面宣言 x 設定の整合判定コア

| 層 | 実体 | 状態 |
|---|---|---|
| 判定コア guest | `aiueos/providerpod_surface.cljk` — route-count は routes の数と一致 / **vars-count は wrangler の vars キーと一致** / kind は閉じた集合 / source path の明示 | **実装済み・実測** |
| 記述子 | `aiueos/providerpod-surface.edn` | **実装済み** |
| parity | `cljs/test/provider_pod/surface_parity_test.cljk` — 宣言（default-db）と **wrangler.jsonc の実データ整合**（ルート数・vars キー集合）を照合 | **実装済み・実測** |

実測: `amu test aiueos/providerpod_surface.cljk` → **15/15**（jvm-kir / js / wasm）。
parity → 3 assertions / 0 failures。

## この repo の点（app-editor と同じ系譜）

- editor / provider-pod は scaffold の stale 値を wrangler.jsonc から**訂正**
  （app-tia の landed reference と同じ方針）。contacts / slides はバイト保持。
  面宣言判定は「訂正が正しい」ことを固定する。
- wrangler.jsonc のコメント除去は**行頭のみ**（URL 内の `//` を消さない ——
  editor の実測で確定した罠を回避）。
- 実運用の能力: `registerProvider / listProviders / getProvider / updateHealth /
  listCapabilities / createPod / listPods / getPodStatus`（APP_CAPABILITIES）。

## 未実装境界

- provider / pod レジストリの判定（lifecycle 状態・health 判定）。
- dispatcher Worker（app.ts）と xrpc-proxy（未配線）の判定。
- 面宣言判定の fleet 共通化（contacts / slides / editor / provider-pod の 4 repo）。
- mon（DNA）化、aiueos session 載せ（fleet 共通）。
