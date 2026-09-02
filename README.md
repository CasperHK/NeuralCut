# ⚡ NeuralCut 影智快剪

NeuralCut 是一款專為現代影音創作者打造的**極速、輕量級本地端 AI 整合影片編輯器**。

傳統的 Electron 影片剪輯軟體往往伴隨著巨大的記憶體消耗與 CPU 開銷，而 [YourProjectName] 另闢蹊徑，採用 **Wails (Go)** 作為底層原生核心，指揮高效能的 C/C++ 影像處理解碼線程，前端則搭配**無虛擬 DOM（Virtual DOM-less）的 Solid.js** 響應式架構。這使得應用程式在保持 30MB 左右的極低閒置記憶體佔用的同時，依然能讓多軌道時間軸（Multi-track Timeline）在拖拽與剪輯時鎖定 **60 FPS** 的極致流暢度，將更多的系統資源完全留給高負載的影片渲染與本地 AI 推理。

---

## ✨ 核心特性

- **🚀 零 VDOM 延遲時間軸：** 基於 Solid.js 的細粒度響應式（Fine-grained Reactivity）UI，無論時間軸上有幾百個剪輯片段、音軌或切口，拖動時僅更新特定節點，徹底告別傳統前端框架的 Diff 效能瓶頸。
- **🤖 本地端 AI 矩陣整合：** 
  - **智慧物件去背與追蹤：** 整合本地 ONNX Runtime 運行 *SAM (Segment Anything Model)*，一鍵自動框選影片主體、動態追蹤與完美去背。
  - **AI 自動語音轉字幕：** 內建優化版 *Whisper (ggml)* 引擎，無需上傳雲端，離線狀態下秒速完成多國語言語音辨識並精準生成時間軸字幕。
  - **語音降噪與優化：** 透過神經網絡模型自動分離背景雜音、風切聲，一鍵優化人聲。
- **🎞️ 原生級硬件加速管線：** Go 後端透過 `cgo` 深度绑定高效能 FFmpeg（`libavcodec`, `libavfilter`, `libavformat`），並直接啟用硬體加速解碼/編碼（如 NVIDIA NVENC/NVDEC, Apple VideoToolbox, Intel QuickSync）。
- **📦 極致輕量化：** 放棄了隨包攜帶 Chromium 瀏覽器的做法，打包後的原生二進位檔案體積僅約 20MB 左右，啟動瞬間完成。
- **🔒 隱私至上 (Privacy-First)：** 所有影片解碼、AI 模型推理、轉碼皆在用戶本地設備 100% 離線完成，數據絕不上傳任何第三方雲端。

---

## 🏗️ 架構技術棧

- **前端 UI 介面：** [Solid.js](https://solidjs.com) + TypeScript + Tailwind CSS
- **應用程式外殼與繫結：** [Wails v2/v3](https://wails.io) (Go)
- **多媒體底層引擎：** FFmpeg (C Libraries) 透過 Go-bindings 進行底層調度
- **本地 AI 推理引擎：** ONNX Runtime (C++) / ggml 

---

## 🛠️ 開發環境準備

在開始編譯本專案之前，請確保您的系統已安裝以下工具：

1. **Go 編譯器** (1.21 或更高版本)
2. **Node.js** (v18 或更高版本) 與套件管理器 **pnpm** (推薦) 或 npm
3. **Wails CLI** (請透過以下命令安裝)：
   ```bash
   go install ://github.com
   ```
4. **系統級多媒體與圖形開發頭文件：**
   - 專案需要系統配置好 FFmpeg 共享庫及標頭檔。詳細系統依賴請參考 [Wails 官方環境建置文件](https://wails.iodocs/gettingstarted/introduction)。

---

## 📦 本地開發與建置步驟

1. **複製原始碼：**
   ```bash
   git clone https://github.com[YourProjectName].git
   cd [YourProjectName]
   ```

2. **安裝前端依賴項目：**
   ```bash
   cd frontend
   pnpm install
   cd ..
   ```

3. **啟動開發者模式 (Dev Mode)：**
   ```bash
   wails dev
   ```
   此命令會自動編譯 Go 後端、啟動帶有熱重載 (HMR) 的 Solid.js 前端開發伺服器，並喚起原生的作業系統視窗。你可以直接在視窗中測試，或在瀏覽器打開偵錯網址。

---

## 🏗️ 生產環境打包編譯

若要編譯出針對當前作業系統深度優化的單一獨立生產環境二進位檔案（包含所有圖示與安裝程式）：

```bash
wails build
```
編譯完成後的執行檔將輸出於 `build/bin/` 目錄下。

---

## 🗺️ 專案目錄結構

```text
├── main.go            # 應用程式入口，初始化 Wails 運行時
├── app.go             # 核心後端 Bindings，負責處理前端傳來的影音控制與 AI 請求
├── video/             # Go 影音處理模組 (FFmpeg 封裝與訊號轉發)
├── ai/                # 本地 AI 推理綁定 (ONNX / Whisper 核心調度)
├── frontend/          # Solid.js 前端專案
│   ├── src/
│   │   ├── components/# 時間軸、監視器視窗、音軌控制等 UI 組件
│   │   ├── store/     # 基於 Solid Signals 的細粒度全域狀態管理
│   │   └── App.tsx    # 主介面佈局
│   └── index.html
├── build/             # 圖示、打包設定與輸出的二進位執行檔
└── wails.json         # Wails 專案組態設定設定
```

---

## 🤝 參與貢獻

我們非常歡迎社群開發者一同加入完善這款自由高效的影片剪輯工具！
- 如果你發現了 Bug 或有新的 AI 功能想法，請提交 [Issue](https://github.com[YourProjectName]/issues)。
- 代碼修改請基於 `main` 分支拉出新分支，並提交 Pull Request。

---

## 📄 開源授權

本專案採用 [MIT License](LICENSE) 授權條款。
