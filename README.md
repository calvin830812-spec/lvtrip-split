# 旅行分帳網頁（模板）

多人旅行的分帳工具：每個人各自手機填花費，金額即時雲端同步、自動匯總，
算出每人付了多少、該攤多少、**最少幾筆轉帳就結清**。每筆花費可勾選分攤對象
（四人均分，或只幫特定幾個人代墊）。

- **本檔**：`index.html`（單一檔案，無需安裝）
- **首版線上案例**：綠島遊 https://calvin830812-spec.github.io/lvtrip-split/
- **後端**：kvdb.io（帳號 `calvin830812@gmail.com`，**已 email 驗證**）

---

## 下次要做「另一趟」的分帳，3 步驟

### 1. 複製並改設定
複製 `index.html`，打開找到最上面 `▼▼▼ 下次重複使用 ▼▼▼` 區塊，改 3 樣：

| 項目 | 說明 | 範例 |
|---|---|---|
| `KEY` | **換一個新代碼**，每趟一個，資料才不會跟上次混到 | `"japan2026"` |
| `NAMES` | 這次的成員（2~N 人都行） | `["阿明","小美","Ken","莉莉","Tom"]` |
| `COLORS` | 對應顏色，數量跟 NAMES 一樣 | 照原本加減即可 |
| `BUCKET` | **不用改**，沿用同一個已驗證帳本 | 保持原值 |

> 為什麼 BUCKET 不用改：kvdb 帳號已驗證，**同一個 BUCKET 配不同 KEY** 就是不同帳本，
> 新 KEY 第一次寫入會自動建立，不需要再驗證任何信。

### 2. 上線
兩種任選：
- **同一個 repo 另開一頁**：把改好的檔存成 `japan.html` 推到 `lvtrip-split` repo，
  網址就是 `…github.io/lvtrip-split/japan.html`（最省事）。
- **開新 repo**：
  ```bash
  gh repo create <新名稱> --public --source=. --remote=origin --push
  gh api -X POST "repos/<帳號>/<新名稱>/pages" -f "source[branch]=main" -f "source[path]=/"
  ```

### 3. 把網址丟群組
大家開同一條網址即可共用同一份帳本。

---

## 技術備忘（給未來的自己）

- **均分/分攤演算法**：每筆 amount 平均分給該筆 `sharers`（沒填 sharers＝全員均分，向後相容）。
  結餘 = 付出 − 該攤；結算用貪婪法湊最少轉帳筆數（已驗證守恆）。
- **同步**：每 4 秒輪詢 GET；新增/刪除用「讀-改-寫」降低互相覆蓋。
- **後端選型教訓**（別再踩）：
  - ❌ jsonblob.com → **沒 CORS**（瀏覽器擋），只能 curl
  - ❌ npoint / jsonsilo → 不能匿名建立
  - ❌ extendsclass → CORS 好但建立要 API key
  - ✅ kvdb.io → CORS 完整、可建桶；唯一門檻是帳號要 email 驗證一次（已完成）
- **kvdb 限制**：免費試用 14 天、每日 1000 次存取、閒置會清。
  旅程短期夠用；要長期穩定可改 **Firebase Realtime DB**（永久免費、anon key 可公開）。
- **隱私**：BUCKET 寫在前端，知道網址者皆可改 → 只適合朋友分帳，勿放機密。
