# 資料結構規格

## Record（掃描記錄）

### 欄位定義（所有版本共用）
```typescript
interface Record {
  barcode:  string   // 13碼條碼，純數字字串
  pinghao:  string   // 品號，大寫，如 "A00016"
  qty:      number   // 數量，正整數，預設 1
  time:     string   // 掃描時間，格式 "HH:MM:SS"
  date?:    string   // 掃描日期，格式 "YYYY-MM-DD"（v2+ 新增）
  photo?:   string   // 照片 URL 或 R2 key（v2+ 新增）
  deviceId? string   // 裝置識別碼（v2+ 新增）
}
```

**重要：欄位名稱不可更改**，跨版本必須一致。

---

## v1-offline 儲存

- 媒介：`localStorage`
- Key：`barcode_records`
- 值：`JSON.stringify(Record[])`
- 無過期機制（手動清除）

---

## v2-cf-pages 資料庫（Cloudflare D1）

### 表格：`records`
```sql
CREATE TABLE records (
  id        INTEGER PRIMARY KEY AUTOINCREMENT,
  barcode   TEXT    NOT NULL,
  pinghao   TEXT    NOT NULL DEFAULT '(未設定)',
  qty       INTEGER NOT NULL DEFAULT 1,
  time      TEXT    NOT NULL,               -- HH:MM:SS
  date      TEXT    NOT NULL,               -- YYYY-MM-DD
  photo_key TEXT,                           -- R2 object key
  device_id TEXT,
  created_at INTEGER NOT NULL               -- Unix timestamp (ms)
);

CREATE INDEX idx_date     ON records(date);
CREATE INDEX idx_pinghao  ON records(pinghao);
CREATE INDEX idx_barcode  ON records(barcode);
```

### R2 照片儲存
- Bucket 名稱：`barcode-photos`
- Key 格式：`{date}/{barcode}.jpg`（例如 `2026-04-18/1150418000001.jpg`）
- 照片在上傳後 record 的 `photo_key` 欄位更新

---

## v2 API 端點規格

### POST `/api/records` — 新增記錄
```json
Request:
{
  "barcode":  "1150418000001",
  "pinghao":  "A00016",
  "qty":      1,
  "time":     "10:23:45",
  "deviceId": "PDA-01"
}

Response 200:
{
  "id":      42,
  "barcode": "1150418000001",
  "pinghao": "A00016",
  "qty":     1,
  "time":    "10:23:45",
  "date":    "2026-04-18"
}
```

### GET `/api/records?date=2026-04-18` — 查詢當日記錄
```json
Response 200:
{
  "records": [ ...Record[] ],
  "summary": [
    { "pinghao": "A00016", "count": 45, "totalQty": 45 }
  ],
  "total": 619
}
```

### GET `/api/records/export?date=2026-04-18` — 下載 Excel
- 回傳 `.xlsx` 二進位，格式同 business-rules.md 匯出規則

### POST `/api/photos/{barcode}` — 上傳照片
```
Content-Type: image/jpeg
Body: binary JPEG data

Response 200:
{ "photo_url": "https://..." }
```

---

## 匯出資料結構（Excel）

| 欄 | 欄位名稱 | 型別 |
|----|---------|------|
| A  | barcode | 文字 |
| B  | pinghao | 文字 |
| C  | qty     | 數字 |

- 無標題列
- 工作表名稱：`工作表1`
- 其他欄位（time、date、photo、deviceId）**不出現在匯出檔案**

---

## 版本遷移說明

v1 → v2 資料遷移：
1. 從 localStorage 讀取 Record[]
2. 補上 `date`（從 barcode 前7碼解析）和 `device_id`（預設 "imported"）
3. 批次 POST 到 `/api/records`
