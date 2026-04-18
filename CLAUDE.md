# Barcode Excel Counter — AI 開發規範

## 專案背景
倉庫出貨條碼掃描記錄系統。掃描槍模擬鍵盤輸入，操作者掃描後產出 Excel 出貨件數報表。

客戶：（範例資料：`出貨件數_115.04.17.xlsx`）

---

## 重要：開始任何版本前必讀

所有版本共用相同的商業規則、UI 規則、資料結構。**規格檔案是唯一的真相來源**，程式碼必須服從規格，不可自行發明規則。

| 規格檔案 | 內容 |
|---------|------|
| `specs/business-rules.md` | 條碼格式、驗證規則、匯出規則、數量規則 |
| `specs/ui-rules.md`       | 顏色、互動行為、元件規範、動畫 |
| `specs/data-schema.md`    | Record 欄位定義、儲存格式、API 介面 |

---

## 版本架構

```
versions/
├── v1-offline/     ← 單一 HTML，localStorage，無需伺服器
├── v2-cf-pages/    ← Cloudflare Pages + D1 + R2，多人同步，支援照片
└── v3-api/         ← RESTful API，外部系統整合
```

### 各版本技術棧

| 版本 | 前端 | 後端/儲存 | 照片 | 多人 |
|-----|------|---------|------|------|
| v1-offline | 單一 HTML + SheetJS | localStorage | ✗ | ✗ |
| v2-cf-pages | HTML/JS on CF Pages | CF Workers + D1 (SQLite) | R2 | ✓ |
| v3-api | 任意前端 | RESTful API | S3/R2 | ✓ |

---

## 新版本開發流程（給 AI 的指示）

1. 讀 `specs/business-rules.md` → 實作相同驗證邏輯
2. 讀 `specs/ui-rules.md` → 套用相同顏色、互動行為
3. 讀 `specs/data-schema.md` → 使用相同欄位名稱
4. 讀對應版本的 `versions/vX-xxx/README.md` → 了解技術限制與需求
5. 參考 `versions/v1-offline/scanner.html` → 了解已驗證的 UX 流程
6. 實作，不可偷換欄位名稱或改變匯出格式

---

## 語言規則
- 所有 UI 文字：繁體中文
- 程式碼變數/函式：英文
- 規格文件：繁體中文

## 禁止事項
- 不可更改 Record 欄位名稱（barcode / pinghao / qty / time）
- 不可更改 Excel 匯出的欄位順序和工作表名稱
- 不可自行新增未在規格中定義的驗證規則
- 不可使用不同主色（必須是 #1a56db）
