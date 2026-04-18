# v2 — Cloudflare Pages + D1 + R2

## 目標
- 多台 PDA 同時掃描，資料即時同步
- 支援拍照（每件貨品附照片）
- 當日紀錄可在辦公電腦即時查看

## 技術棧
| 層 | 服務 | 用途 |
|----|------|------|
| 前端 | Cloudflare Pages | HTML/JS 靜態頁面 |
| API | Cloudflare Workers | 商業邏輯、D1 操作 |
| 資料庫 | Cloudflare D1 | SQLite，儲存 records |
| 照片 | Cloudflare R2 | 物件儲存，圖片檔案 |

## 目錄結構
```
v2-cf-pages/
├── public/
│   └── index.html        ← 前端（與 v1 相同 UI 規則）
├── worker/
│   └── index.js          ← Cloudflare Worker（API）
├── schema.sql            ← D1 建表 SQL
└── wrangler.toml         ← Cloudflare 設定
```

## API 端點
詳見 `specs/data-schema.md`

## 開發前提示（給 AI）
1. 讀 `specs/business-rules.md` — 驗證規則必須與 v1 完全一致
2. 讀 `specs/ui-rules.md` — 套用相同顏色與互動規則，額外加同步狀態指示器
3. 讀 `specs/data-schema.md` — 使用定義的 D1 schema 和 API 格式
4. 參考 `versions/v1-offline/scanner.html` — 前端 UX 流程不可退化

## 狀態
🔲 待實作
