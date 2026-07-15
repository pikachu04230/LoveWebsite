# LoveWebsite - W3 優化 Backlog (Optimization Backlog)

以下為根據 W3 專案健檢結果，依優先序排列的未來優化項目（Backlog）：

## [P0] 修復 Page 4 記憶體洩漏漏洞
- **目標**：確保全域事件被正確移除，避免記憶體與效能浪費。
- **作法**：實作 `skipToPage5` 函式，在呼叫 `showPage('page5')` 跳頁前，強制呼叫解鎖邏輯的 `cleanup()` 來解除 `mousemove`, `mouseup`, `touchmove`, `touchend` 的事件綁定。

## [P1] 重構「逃跑按鈕」邏輯與無障礙修復
- **目標**：消除程式碼重複 (DRY 原則)、修復越界失效與無障礙操作阻斷。
- **作法**：
  1. 將 Page 1 和 Page 6 的逃跑按鈕 (`doEscape`, `doEscape2`) 邏輯封裝為共用 Function 或是 Class，傳入指定的 DOM 元素與邊界元素即可運作。
  2. 移除 `btnNo.addEventListener('focus', doEscape)` 的綁定，保留單純的點擊與觸控逃跑。
  3. 實作嚴格的邊界 `Clamp` 機制，確保按鈕座標落在 `(0, window.innerWidth - buttonWidth)` 安全範圍內。

## [P2] CSS 樣式清理與模組化
- **目標**：落實關注點分離，消除過度使用的 Inline Styles。
- **作法**：
  - 將 JS 中動態設定的按鈕投降狀態（`background`, `color`, `box-shadow` 等）移回 HTML `<style>` 區塊建立如 `.btn-no.surrendered` 等 class，JS 端僅呼叫 `classList.add('surrendered')` 控制。

## [P3] 動畫效能優化 (Layout Thrashing)
- **目標**：減少瀏覽器的重繪 (Repaint) 與重排 (Reflow)，維持 60 FPS 流暢度。
- **作法**：
  - 逃跑按鈕的位移從直接修改 `style.left` 與 `style.top`，改用硬體加速的 `transform: translate(x, y)`。
  - 將不變的邊界資訊 (`getBoundingClientRect`) 進行快取，僅在 `window.resize` 時更新，避免高頻率呼叫。

## [P3] 架構與狀態管理重構 (未來擴充準備)
- **目標**：若頁數將擴增至 10 頁以上，現有的 HTML 全集中與全域變數機制將難以維護。
- **作法**：
  1. 將 CSS 抽離為 `styles.css`，JS 抽離為 `main.js`。
  2. 建立全域的 `Store` 物件或引入前端框架（如 Vue / React）來統一管理各頁面的狀態與選擇。
  3. 考慮實作基於 History API 的真實前端路由，允許使用者使用瀏覽器的上一頁與重整功能。
