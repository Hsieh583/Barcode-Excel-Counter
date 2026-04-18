# 條碼掃描出貨記錄系統

倉庫出貨作業工具。掃描槍掃描貨品條碼，系統自動彙整並產出 Excel 出貨件數報表。

## 版本一覽

| 版本 | 說明 | 狀態 | 前端 | 儲存 | 多人 | 照片 |
|------|------|------|------|------|------|------|
| [v1-offline](versions/v1-offline/) | 單一 HTML 離線版 | ✅ 運行中 | 單一 HTML | localStorage | ✗ | ✗ |
| [v2-cf-pages](versions/v2-cf-pages/) | Cloudflare 多人同步版 | 🔲 規劃中 | CF Pages | CF Workers + D1 | ✓ | R2 |
| [v3-api](versions/v3-api/) | RESTful API 整合版 | 🔲 規劃中 | 任意 | CF Workers + D1 | ✓ | R2 |

首頁入口：開啟 [index.html](index.html) 可看到所有版本的運行連結與狀態。

---

## 快速開始（v1 離線版）

1. 下載 [`versions/v1-offline/scanner.html`](versions/v1-offline/scanner.html)
2. 用瀏覽器直接開啟（不需伺服器）
3. 在「品號」欄填入目前品項代碼（如 `A00016`）
4. 對著掃描槍掃描貨品，條碼自動加入列表
5. 完成後按「下載 Excel」產出報表

> PDA 使用方式：USB 拷貝 `scanner.html` 到裝置，用 Chrome 開啟即可。

---

## 條碼格式

```
1150417000205
│││││││╰──────  流水號（6碼，當日從 000001 起）
││││╰╰╰──────  日期（2碼）
││╰╰──────────  月份（2碼）
╰╰────────────  民國年（3碼，115 = 西元 2026）
```

---

## 專案結構

```
/
├── index.html              # 版本入口首頁
├── versions.json           # 版本設定（新增版本修改此檔）
├── CLAUDE.md               # AI 開發規範
├── specs/
│   ├── business-rules.md   # 條碼格式、驗證、匯出規則
│   ├── ui-rules.md         # 顏色、互動行為、元件規範
│   └── data-schema.md      # Record 欄位定義與 API 介面
└── versions/
    ├── v1-offline/
    │   ├── README.md
    │   └── scanner.html    # ✅ 可直接使用
    ├── v2-cf-pages/
    │   └── README.md
    └── v3-api/
        └── README.md
```

---

## 規格文件

所有版本共用相同規格，**規格是唯一的真相來源**，程式碼必須服從規格。

| 檔案 | 內容 |
|------|------|
| [specs/business-rules.md](specs/business-rules.md) | 條碼格式、驗證規則、匯出格式、統計規則 |
| [specs/ui-rules.md](specs/ui-rules.md) | 主色 `#1a56db`、互動行為、元件規範 |
| [specs/data-schema.md](specs/data-schema.md) | `barcode / pinghao / qty / time` 欄位定義 |

---

## 新版本開發

1. 在 `versions/` 新建目錄並實作
2. 在 `versions.json` 加入版本資訊（填入 `deployedUrl` 後首頁自動顯示連結）
3. 遵循 `CLAUDE.md` 中的 AI 開發規範，確保跨版本一致性

---

## 技術說明

- **匯出格式**：欄位順序 `條碼 → 品號 → 數量`，無標題列，工作表名稱固定為 `工作表1`
- **檔名格式**：`出貨件數_民國年.月.日.xlsx`（例如 `出貨件數_115.04.18.xlsx`）
- **UI 主色**：`#1a56db`（所有版本一致）
