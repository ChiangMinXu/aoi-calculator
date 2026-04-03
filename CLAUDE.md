# AOI 光學架構評估計算器 — 開發交接文件

## 專案概覽

這是一個**純前端單頁應用**（無後端、無框架），讓光學工程師評估 AOI（自動光學檢測）系統的鏡頭與相機組合。
所有程式碼集中在 **`index.html`**（約 2160 行），使用原生 HTML + CSS + JavaScript。

- **GitHub**: https://github.com/ChiangMinXu/aoi-calculator.git
- **部署**: GitHub Pages（push 到 `main` 即自動上線）
- **資料持久化**: `localStorage`（無資料庫、無登入）

---

## 架構

### 兩大計算模式（`S.mode`）

| 模式 | 說明 | 主要函數 |
|------|------|---------|
| `'scan'` | 掃描取像（相機沿樣品掃描） | `calculate(p)` → `calc()` |
| `'static'` | 靜態取像（相機固定覆蓋樣品） | `calculateStatic(p, ss)` → `calcStatic()` |

模式切換由 header 的 segment button 控制：`setMode('scan' | 'static')`

### 全域狀態 `S`（`index.html:864`）

```js
let S = {
  lineScan: false,       // 線掃 vs 面掃相機
  color: false,          // 彩色 vs 黑白
  binning: false,
  lensType: 'objective', // 'objective' | 'telecentric' | 'cctv'
  scanAlongWidth: true,
  scanMode: 'single',
  mode: 'scan'           // 'scan' | 'static'
};
```

### 資料來源

- 內建相機/鏡頭清單：`CAMS[]`、`LENS[]`（檔案中的 JS 陣列）
- 使用者自訂：`custCams`、`custLens`（localStorage）
- 隱藏內建條目：`hiddenCams`、`hiddenLens`（Set，存 localStorage）
- 儲存的評估紀錄：`evals`（localStorage，key: `aoi_evals`）

---

## 重要 UI 區塊 ID

| ID | 說明 |
|----|------|
| `#pane-res` | 掃描模式結果面板 |
| `#pane-static-res` | 靜態模式結果面板 |
| `#pane-db` | 相機/鏡頭資料庫面板 |
| `#pane-hist` | 歷史評估面板 |
| `#left` | 左側輸入欄 |
| `#right` | 右側結果欄 |
| `dg` | 掃描模式示意圖 canvas |
| `static-dg` | 靜態模式示意圖 canvas |

---

## 列印 / 匯出 PDF

`exportPDF()` 根據當前模式在 `<body>` 加 `print-scan` 或 `print-static` class，
`@media print` 的 CSS 依此決定顯示哪個面板、隱藏另一個。

```js
// index.html:1792
function printWithMode(mode){
  document.body.classList.remove('print-scan','print-static');
  document.body.classList.add(mode==='static'?'print-static':'print-scan');
  window.print();
  setTimeout(()=>document.body.classList.remove('print-scan','print-static'),1000);
}
```

---

## 開發慣例

- 所有改動都在 `index.html` 這一個檔案
- commit 後直接 `git push`，GitHub Pages 自動部署
- commit 訊息用繁體中文描述功能，英文前綴（`feat:` / `fix:` / `refactor:`）
- 不加文件、不建額外檔案，保持單檔結構

---

## 近期主要功能（最新在上）

1. **靜態模式 PDF 修正** — 列印時只顯示當前模式內容
2. **重疊率 0 修正** — 輸入 0 不再被當成空值套用預設 10%
3. **靜態模式拖曳排序修正**
4. **靜態取像模式** — 多相機覆蓋佈局、示意圖、PDF 匯出
5. **三色燈警示** — 系統解析度以餘裕倍數為基準
6. **手機響應式佈局** — 側滑抽屜
7. **總覽小卡自訂** — 可隱藏、可拖曳排序
8. **資料庫編輯功能** — 自訂相機/鏡頭可直接修改

---

## 快速開始

```bash
git clone https://github.com/ChiangMinXu/aoi-calculator.git
cd aoi-calculator
# 直接用瀏覽器開啟 index.html，或：
python3 -m http.server 8080
```
