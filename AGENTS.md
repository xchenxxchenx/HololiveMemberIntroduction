# Hololive 知識庫維護手冊 (AGENTS.md)

本知識庫遵循 Andrej Karpathy 的 **LLM Wiki** 模式設計，作為一個具備「持續積累、跨頁面自動關聯、演進式知識綜合」的 Hololive Production 專屬知識庫。
LLM Agent 是此知識庫的專屬維護者（The Programmer / Maintainer），使用者則是知識的引導者、探索者與決策者。

---

## 1. 核心定位與三層架構 (Architecture)

知識庫嚴格劃分為三個層次：

```text
Hololive介紹/
├── AGENTS.md                  # 【Schema 手冊】維護規範、命名約定與工作流（本文件）
├── index.md                   # 【目錄導覽】內容導向的總索引，記錄全站結構
├── log.md                     # 【歷史日誌】時間序列日誌（append-only）
├── raw/                       # 【第一層：原始來源 (Raw Sources)】不可篡改的真實數據源
│   └── sources/               # 官方資料、維基剪輯、訪談報導、直播存檔摘要
├── wiki/                      # 【第二層：知識網絡 (The Wiki)】由 LLM 全權建立與維護的 Markdown 庫
│   ├── talents/               # 個人檔案（藝人、工作人員）
│   ├── generations/           # 期別與分支組織
│   ├── units/                 # 正式/非正式組合與團體
│   ├── events/                # 大型活動、聯動企劃與重要賽事
│   └── lore_and_memes/        # 社內梗、重要設定、伺服器歷史
└── asset/                     # 【第三層：靜態資源】
    └── picture/               # 藝人立繪、頭像縮圖、官方 Logo（供後續 HTML 簡報使用）
```

1. **Raw Sources (`raw/sources/`)**：只讀數據源。包含官方資料摘錄、Fan Wiki 抓取、新聞訪談等。一旦存入不可修改。
2. **The Wiki (`wiki/`)**：LLM 擁有的知識產出層。LLM 負責創建頁面、雙向鏈結、衝突解決與跨頁同步更新。
3. **The Schema (`AGENTS.md`)**：規範 LLM 如何操作、寫入與維護整個知識庫。

---

## 2. 命名與語言規範 (Conventions)

1. **主要語言**：內文採用**繁體中文**。
2. **名稱標準化格式**：
   - 頁面標題與正文初次提及時，統一採用：`中文譯名 (English Name / 日文原名)`
   - 範例：`# 兔田佩克拉 (Usada Pekora / 兎田ぺこら)`
3. **檔案命名**：
   - 全面使用**小寫羅馬拼音 / 英文連字號（kebab-case）**，禁止使用中文檔名以確保跨平台與 CLI 兼容性。
   - 個人檔案：`wiki/talents/usada-pekora.md`、`wiki/talents/gawr-gura.md`、`wiki/talents/yagoo.md`
   - 期別檔案：`wiki/generations/jp-3rd.md`、`wiki/generations/en-myth.md`
   - 組合檔案：`wiki/units/micomet.md`、`wiki/units/smok.md`
   - 事件檔案：`wiki/events/holo-gta-2024.md`、`wiki/events/holofes-5th.md`
   - 梗與歷史：`wiki/lore_and_memes/usada-kensetsu.md`、`wiki/lore_and_memes/elite-english.md`
4. **雙向鏈結規則（Obsidian 語法）**：
   - 內部引用必須使用 Obsidian 雙向鏈結格式：`[[檔案名(不含副檔名)|顯示文字]]`。
   - 範例：`與 [[sakura-miko|櫻巫女]] 組成熱門搭檔 [[micomet|MiComet]]。`
   - 必須確保雙向互通（當 A 提及 B，B 頁面也應具備相關聯動脈絡）。

---

## 3. Schema 與 Frontmatter 規範

為了支援後續**自動轉換為 HTML 藝人簡報/介紹卡片**，所有 Wiki 頁面必須包含標準化 YAML Frontmatter。

### 3.1 藝人與工作人員個人檔案 (`wiki/talents/*.md`)
```yaml
---
name_zh: "兔田佩克拉"
name_jp: "兎田ぺこら"
name_en: "Usada Pekora"
branch: "Hololive JP"
generation: "3rd Generation (HoloFantasy)"
status: "active" # 可選值: active | graduate | Breakoff | resign
debut_date: "2019-07-17"
graduation_date: null # 若為 graduate 或 Breakoff 則填寫日期，如 "2024-08-28"
fan_name: "野兔 (Nousagi)"
oshi_mark: "👯‍♀️"
illustrator: "憂姬はぐれ"
image: "asset/picture/Usada-Pekora_list_thumb.png" # 嚴格對應 asset/picture 中的圖片檔名
greetings:
  opening: "こんぺこ！こんぺこ！こんぺこ！"
  closing: "おつぺこでした！"
representative_songs:
  - "ぺこらんだむぶれいん！"
  - "全人類　兎化計画！"
tags: [gaming, comedy, vocal, minecraft, holofantasy]
---
```

#### 藝人內文標準章節：
1. **簡介與背景**（設定、出道歷程）
2. **外貌與衣裝特徵**
3. **直播特色與代表風格**（遊戲風格、談話雜談、耐久直播等）
4. **主要關係與組合**（指向 `[[units/...]]` 或其他 `[[talents/...]]`）
5. **重要成就與重大事件**（指向 `[[events/...]]`）
6. **代表梗與名言**（指向 `[[lore_and_memes/...]]`）
7. **參考來源**（引用 `raw/sources/...`）

---

### 3.2 期別與分支檔案 (`wiki/generations/*.md`)
```yaml
---
title_zh: "Hololive 三期生 (HoloFantasy)"
title_en: "Hololive 3rd Generation (HoloFantasy)"
branch: "Hololive JP"
debut_period: "2019-07 ~ 2019-08"
members:
  - "usada-pekora"
  - "shiranui-flare"
  - "shirogane-noel"
  - "houshou-marine"
  - "uruha-rushia" # 標記歷史全體成員
tags: [generation, jp, holofantasy]
---
```

### 3.3 組合檔案 (`wiki/units/*.md`)
```yaml
---
name: "MiComet"
name_zh: "櫻星組 / MiComet"
members:
  - "sakura-miko"
  - "hoshimachi-suisei"
type: "duo" # duo | group | unofficial
established_date: "2019"
tags: [unit, business-partner, gta, micomet]
---
```

---

## 4. 三大核心維護操作 (Operations)

### 4.1 攝入流程 (Ingest)
每當有新資料進來（使用者手動放進 `raw/sources/`，或請 LLM 檢索網路整理存入 `raw/sources/`）：
1. **讀取源文件**：LLM 完整閱讀目標原始來源。
2. **跨頁面傳播更新（Multi-page propagation）**：
   - 提取關鍵資訊，更新所有受影響的 Wiki 頁面（通常單一來源會更新 3~10 個頁面）。
   - 若提及新人物、新梗或新組合且尚未建頁，依規範建立新頁面或在相關頁面埋下待補連結。
   - 若新資訊與舊資訊衝突（例如成員宣布畢業、換衣裝、創下新紀錄），標記時間並更新現狀，保留演變歷程。
3. **維護 `index.md`**：若有新頁面生成，更新目錄索引分類與一句話摘要。
4. **記錄 `log.md`**：在日誌底部追加一筆標準記錄：
   `## [YYYY-MM-DD] ingest | <Source Name> -> updated: [[page1]], [[page2]]`

### 4.2 檢索與綜合 (Query & Synthesis)
當使用者提問（例如：「請比較三期生與 DEV_IS ReGLOSS 的音樂風格」、「整理 Minecraft 兔田建設的完整興衰史」）：
1. **查閱 `index.md` 與目標頁面**：精確定位多個相關 wiki 頁面並閱讀。
2. **綜合回答**：結合交叉引用資料輸出具結構性、條理清晰且帶有 Wiki 雙向鏈結的回答。
3. **知識沉澱（File-back to Wiki）**：若該次回答產生了高價值的深度比較、重大歷史整理或跨期別分析，LLM 應主動詢問使用者或直接將其歸檔為 `wiki/` 內的新文章（如放入 `wiki/lore_and_memes/` 或 `wiki/events/`），讓知識持續複利成長。

### 4.3 健康檢查 (Lint)
定期或在使用者發出指示時執行維護檢查：
1. **檢查死鏈（Broken Links）**：是否有 `[[unknown-page|...]]` 指向不存在的檔案。
2. **檢查孤立頁面（Orphan Pages）**：是否有任何頁面沒有任何反向連結（Inbound link）。
3. **狀態與日期一致性**：現役/卒業/退職狀態標記（`active` / `graduate` / `Breakoff` / `resign`）是否在所有提及處保持一致。
4. **資產校驗**：檢查 `image` 欄位指向的 `asset/picture/...` 是否存在對應圖檔。
5. **在 `log.md` 紀錄檢查結果**：
   `## [YYYY-MM-DD] lint | Healthy (or fixed X broken links)`

---

## 5. HTML 簡報轉化準備規範 (Presentation Readiness)

為了確保日後可以直接無縫生成**高質量 HTML 藝人介紹簡報**：
1. 藝人檔案中必須確保 `image:` 路徑與 `asset/picture/` 內的真實圖片檔名嚴格對照（如 `asset/picture/Usada-Pekora_list_thumb.png`）。
2. `representative_songs`、`greetings`、`oshi_mark` 等欄位必須維持整潔結構化。
3. 簡報生成器（待開發）將直接解析 `wiki/talents/*.md` 的 Frontmatter 與精選段落，套入客製化 CSS 簡報模板呈現。
