# PC Spec Check

A privacy-conscious, static browser page that displays a small set of device details exposed by the browser. The interface supports **Traditional Chinese** and **English**.

繁體中文：這是一個以靜態網頁呈現的瀏覽器裝置資訊概覽，支援繁體中文與英文，僅讀取瀏覽器允許網站取得的有限資訊。

## Features / 功能

- Shows browser-exposed logical processor count, estimated device memory, WebGL renderer (when available), network hints, screen dimensions, and a best-effort OS/browser identification.
- Displays an **information availability** score based on how many categories can be read. It is not a performance score or hardware benchmark.
- Switches between Traditional Chinese and English without reloading.
- Handles missing, malformed, unsupported, or privacy-restricted browser APIs with fallback labels rather than stopping the entire page.
- Uses `textContent` for browser-provided strings; no external JavaScript, CSS, fonts, analytics, or API calls are required by the page.

## Run locally / 本機執行

1. Download or clone this repository.
2. Open `index.html` in a recent browser. No build step or package installation is required.
3. For a closer match to hosted behavior, serve the repository root with any static HTTP server and open the local URL.

## Deploy with GitHub Pages / 發布到 GitHub Pages

1. Open the repository **Settings → Pages**.
2. Under **Build and deployment**, choose **Deploy from a branch**.
3. Select the `main` branch and `/(root)`, then save.
4. Open the Pages URL shown in the settings after deployment completes.

## What the page reads / 讀取的資訊

| Information | Browser API / method | Notes |
| --- | --- | --- |
| Logical processors | `navigator.hardwareConcurrency` | May be reduced or omitted by the browser for privacy or resource-management reasons. It is not a CPU model. |
| Estimated device memory | `navigator.deviceMemory` | Optional, rounded/coarsened estimate; not current memory use. |
| Graphics renderer | WebGL and `WEBGL_debug_renderer_info` | Optional and often restricted. Renderer strings can reveal device details, so this value is displayed locally in the page only. |
| Network status/details | `navigator.onLine`, optional `navigator.connection` | Connection details are not supported consistently and are only estimates. |
| Screen | `screen.width`, `screen.height`, `devicePixelRatio` | Values describe the current screen/browser environment, not necessarily the panel's native resolution. |
| OS/browser | User-Agent and platform hints | Best-effort identification only; browsers may reduce or change these values. |

## iPad and Safari identification / iPad 與 Safari 辨識

The page checks explicit iPad/iPhone User-Agent tokens and platform hints. iPadOS Safari in desktop mode may identify itself as a Mac; a Mac-like platform combined with multiple touch points and a Safari token is treated as iPadOS. Safari detection runs only after excluding common Chrome, Edge, Opera, and Firefox User-Agent tokens. These are heuristics, not guaranteed device identification; private browsing, User-Agent reduction, embedded browsers, and future browser changes can affect results.

頁面會優先檢查 iPad/iPhone 的 User-Agent 與平台資訊。iPadOS Safari 桌面模式可能呈現 Mac 資訊；若同時符合 Mac 平台、觸控點數及 Safari 樣式的 User-Agent，會推測為 iPadOS。Safari 判斷會先排除常見的 Chrome、Edge、Opera 與 Firefox 標記。這些只是推測，無法保證識別所有裝置或瀏覽器。

## Score / 分數說明

The 0–100 score is the percentage of four categories available to the page: CPU count, memory estimate, GPU renderer, and screen dimensions. Network status/details are displayed separately and are not included in the score. It measures **information availability only**. It does not measure CPU/GPU speed, actual installed RAM, thermals, storage, or real-world performance, and should not be used to compare devices.

0–100 分是四類資訊（CPU 邏輯處理器數、記憶體估值、GPU 渲染器、螢幕尺寸）中可讀取項目的比例；網路狀態／詳細資訊會另外顯示，不納入分數。它只表示「資訊可用度」，不代表 CPU/GPU 速度、實際 RAM 容量、溫度、儲存空間或實際效能，不適合用來比較裝置。

## Privacy and security / 隱私與安全

- The page does not intentionally send the displayed device information to a third-party service. It has no application backend or analytics code.
- The Content Security Policy disallows external resource connections and form submissions. GitHub Pages or another web host may still receive ordinary request metadata needed to serve the page; consult the hosting provider's privacy documentation for its handling of logs.
- The optional full User-Agent display is hidden until the user chooses to reveal it. Avoid sharing screenshots or copied details if they contain information you consider sensitive.
- WebGL renderer information can be identifying. The page uses it only for the on-page availability indicator and display; it is not submitted elsewhere by the page.

本頁沒有應用程式後端或分析程式，也不會刻意將顯示的裝置資訊傳送給第三方。CSP 會限制外部資源連線與表單提交，但 GitHub Pages 或其他網站主機仍可能收到提供網頁所需的一般請求資訊；主機記錄的處理方式請參閱其隱私文件。完整 User-Agent 預設隱藏，分享截圖或資訊前請先確認內容。

## Limitations and troubleshooting / 限制與排解

- `Not provided` or an unavailable label usually means the browser does not implement, exposes, or permits access to that API. It does not necessarily indicate a device problem.
- GPU information may be hidden by browser privacy settings, graphics-driver configuration, WebGL restrictions, or a headless environment.
- A displayed OS or browser may be unknown or inaccurate because User-Agent values are intentionally reduced or changed by browsers.
- The page is a static client-side overview, not a diagnostic utility. It cannot read full hardware models, storage capacity, live RAM use, temperatures, or benchmark results.
- If the page does not load correctly, try a current browser with JavaScript enabled, reload the page, or open the browser console to inspect script errors. No installation or account is required.

## Development / 開發

This repository is intentionally dependency-free: `index.html` contains the markup, styles, and JavaScript. Review changes in a browser at desktop and mobile sizes, and test with APIs unavailable where possible. Do not treat User-Agent or browser-provided hardware hints as authoritative or use them for security decisions.


## New tools / 新增功能

- Device type detection: desktop, laptop, tablet, or phone (best effort).
- Browser storage estimate using `navigator.storage.estimate()`; this is origin storage, not free system disk space.
- Battery status when `navigator.getBattery()` is supported.
- Browser capability summary for secure context, WebGL, storage estimation, and clipboard.
- Local plain-text report download; report generation stays in the browser.
