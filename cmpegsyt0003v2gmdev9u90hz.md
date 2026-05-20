---
title: "Obsidian MCP 三種工具實戰比較"
datePublished: 2026-05-20T19:38:40.312Z
cuid: cmpegsyt0003v2gmdev9u90hz
slug: obsidian-mcp

---

---
tags:
  - mcp
  - obsidian
  - mcp/comparison
  - mcp/analysis
  - obsidian/plugin
  - obsidian-blogger/post
  - obsidian-blogger/draft
profileName: Just E!
postId: "7373519934638137794"
url: https://elf-here-us.blogspot.com/
blogger:
  published: 2026-05-21T02:25:41+08:00
  updated: 2026-05-21T02:25:41+08:00
---

# Obsidian MCP 三種工具實戰比較

> 本文由 AI（@bluelovers/opencode-arise / Shadow Monarch）協助撰寫，內容基於實際工具測試結果。建議讀者在使用前自行驗證最新版本的功能變化。

> 從外掛到純本地，三種串接 Obsidian 與 MCP 的方式各有什麼優劣？

如果你正在研究如何讓 AI Agent 讀寫 Obsidian 筆記，可能會發現市面上至少有三種不同的 MCP 工具。它們名字很像，底層技術也相似，但實際用起來卻各有取捨。

這篇文章會從**實際測試的角度**，帶你快速看懂這三個工具的定位、能力與限制。

---

## 三個工具一句話版

| 工具 | 一句話 |
|------|--------|
| **obsidian-local-rest-api** | 裝在 Obsidian 裡的外掛，開啟 REST API + MCP 兩種通道 |
| **mcp-obsidian** | 包裝上面那個外掛的 MCP 介面，定位是 MCP-only |
| **mcpvault** | 反其道而行 — 不靠外掛，直接指定本機路徑讀寫 vault |

---

## 1. obsidian-local-rest-api — 最完整的 API 通道

- GitHub：https://github.com/coddingtonbear/obsidian-local-rest-api
- 本質：**Obsidian 外掛**，安裝後在 Obsidian 內啟動一個 HTTP 伺服器
- 支援：**REST API** + **MCP** 兩種模式
- 通訊：支援 **HTTP** 與 **HTTPS**（但 HTTPS 自簽憑證在某些環境下會有問題）

### 實測心得

這是我測試過**功能最完整**的選項。因為它直接暴露 Obsidian 的底層 API，所以能做的事情最多：

- 讀寫任何檔案類型（`.md`、`.json`、圖片等）
- 精確定位編輯（heading / block reference / frontmatter 三種 target）
- 可編輯 heading 行本身（`targetScope: marker`）
- 可執行 Obsidian 內部指令
- 可取得當前開啟的檔案
- 可在 UI 中開啟檔案
- 可搜尋全文（支援 JsonLogic 結構化查詢）

但也有一些麻煩的地方：

1. **需要 Obsidian 在背景執行** — 如果 Obsidian 沒開，工具就直接斷線
2. **需要安裝外掛** — 對某些管控嚴格的環境可能不便
3. **HTTPS 自簽憑證問題** — 像我的環境就遇到 `SSE error: self signed certificate`，最後只能切 HTTP 模式使用

### 小結

如果你想找一個**功能最強、操作最靈活**的方案，這就是你要的。代價是你必須讓 Obsidian 一直開著，而且得接受那個自簽憑證的小麻煩。

---

## 2. mcp-obsidian — 純 MCP 包裝層

- GitHub：https://github.com/MarkusPfundstein/mcp-obsidian
- 本質：**MCP 伺服器**，底層依賴 `obsidian-local-rest-api` 外掛
- 定位：純 MCP 介面，無 REST API

這個工具本質上就是第一項的 **MCP-only 版本**。它做的事情就是幫你把 obsidian-local-rest-api 的 REST 端點包裝成 MCP 工具。

在我們測試的環境中，**已經被新的 mcp-obsidian-local-rest-api 工具集取代** — 新版的工具數量更多、功能更完整，而且直接對應到 REST API 的所有能力。

如果你是新使用者，**直接使用新版工具即可**，不需要回頭用這個舊版包裝。

---

## 3. mcpvault — 不裝外掛的另類選擇

- GitHub：https://github.com/bitbonsai/mcpvault
- 本質：**獨立 MCP 伺服器**，直接指定本機 vault 路徑
- 特色：**不需要安裝任何 Obsidian 外掛**

這是最讓我驚喜的一個選項。它的設計哲學完全不同：

### 它是怎麼運作的？

一般的 MCP 工具需要透過 HTTP 跟某個服務溝通，但 mcpvault 直接讀取你硬碟上的 Obsidian vault 資料夾。你在 MCP 設定檔裡面給它一個路徑，它就自己去處理剩下的。

### 優點

- **不需要 Obsidian 執行中** — 即使 Obsidian 關著，工具照樣能讀寫
- **不需要安裝外掛** — 對某些不允許安裝社群外掛的環境特別有用
- **有移動/重新命名功能** — 這是前兩個工具完全做不到的
- **有標籤管理功能** — 可直接 add/remove/list 標籤，還有全 vault 標籤統計
- **批次讀取** — 一次最多讀取 10 個筆記
- **操作安全機制** — 刪除和移動檔案需要二次確認

### 缺點

- **僅支援 `.md` 檔案** — 想讀 `.json`、圖片？不行
- **沒有精確編輯** — 只能做字串取代，無法定位到特定 heading 或 block
- **沒有 UI 互動** — 不能在 Obsidian 中開啟檔案、不能執行指令
- **限本機** — 無法裝在遠端伺服器上（因為需要直接存取檔案系統）

### 小結

如果你只是想要**批次整理筆記、管理標籤、搬動檔案**，而且不想要 Obsidian 一直開著，mcpvault 是很棒的輕量選擇。但如果你需要**精確編輯內容或與 Obsidian UI 互動**，它就不太夠用了。

---

## 功能對照總表

| 面向 | local-rest-api | mcpvault |
|------|:--------------:|:--------:|
| 需 Obsidian 運行 | ✅ 必須 | ❌ 不需要 |
| 需安裝外掛 | ✅ 必須 | ❌ 不需要 |
| 可裝在遠端 | ✅ 可以 | ❌ 限本機 |
| 建立/覆寫檔案 | ✅ vault_write | ✅ write_note |
| 附加/插入內容 | ✅ vault_append | ✅ write_note 三種 mode |
| 精確 section 編輯 | ✅ vault_patch | ❌ 僅字串取代 |
| 編輯 heading 行 | ✅ targetScope:marker | ❌ 無法 |
| 批次讀取 | ❌ 無 | ✅ read_multiple_notes |
| 移動/重新命名 | ❌ 無 | ✅ move_note + move_file |
| Frontmatter 獨立更新 | ❌ 需 patch | ✅ update_frontmatter |
| 標籤管理 | ❌ 無 | ✅ manage_tags + list_all_tags |
| Vault 統計 | ❌ 無 | ✅ get_vault_stats |
| 檔案結構探索 | ✅ document_map | ❌ 無 |
| 執行 Obsidian 指令 | ✅ command_execute | ❌ 無 |
| UI 開啟檔案 | ✅ open_file | ❌ 無 |
| 取得當前檔案 | ✅ active_file_path | ❌ 無 |
| 週期筆記 | ✅ periodic_note | ❌ 無 |
| 非 `.md` 檔案 | ✅ 可以 | ❌ 僅限 `.md` |
| 操作安全確認 | ❌ 無 | ✅ 二次確認機制 |

> 註：`mcp-obsidian`（舊版）已被新版 `mcp-obsidian-local-rest-api` 取代，功能上完全被新工具覆蓋，故不特別列入比較。

---

## 所以該選哪一個？

這其實不是選擇題。這兩個工具是**互補關係**，而不是競爭關係：

```
你需要做的事                 → 推薦工具
──────────────────────────────────────────
精確編輯筆記內容              → local-rest-api
搜尋與讀取筆記                → 兩者皆可
批次搬移/重新命名大量筆記      → mcpvault
管理筆記標籤                  → mcpvault
與 Obsidian UI 互動           → local-rest-api
操作 JSON 或其他非 md 檔案    → local-rest-api
不開 Obsidian 也能讀寫        → mcpvault
需要遠端存取 vault            → local-rest-api
```

如果你只能選一個，我會建議先以 **local-rest-api** 為主 — 因為它的功能涵蓋面最廣，精確編輯的能力是 mcpvault 無法取代的。等真的遇到需要搬檔案或整理標籤的情境時，再把 mcpvault 補上就好。

---

## 附錄：關於 HTTPS 自簽憑證

如果你跟我一樣遇到 `SSE error: self signed certificate`，這裡有幾個解法：

1. **切換 HTTP 模式**（最簡單） — 在 MCP 設定中把 protocol 改為 `http`，port 改為 `27123`
2. **將自簽憑證加入信任**（較安全） — 匯出 obsidian-local-rest-api 產生的憑證，加入系統信任清單
3. **使用環境變數跳過驗證**（不建議） — `NODE_TLS_REJECT_UNAUTHORIZED=0`，僅限開發環境

---

## 參考連結

- [obsidian-local-rest-api](https://github.com/coddingtonbear/obsidian-local-rest-api) — Obsidian 外掛，提供 REST + MCP
- [mcp-obsidian](https://github.com/MarkusPfundstein/mcp-obsidian) — MCP 包裝層（已被新版取代）
- [mcpvault](https://github.com/bitbonsai/mcpvault) — 獨立 MCP 伺服器，直接讀寫本機 vault

