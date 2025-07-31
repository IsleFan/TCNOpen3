# TCNOpen3 架構與使用說明

TCNOpen3 是一套以 C 語言實作的 TRDP (Train Real-time Data Protocol) 開源堆疊，提供跨平台的即時資料通訊能力。
本文件概述專案的主要結構與使用方式。

## 目錄結構

- `src/`：核心程式碼
  - `api/`：公開的標頭檔，例如 `trdp_if_light.h`、`trdp_types.h`
  - `common/`：TRDP 核心實作，包含 `trdp_pdcom.c`、`trdp_mdcom.c`、`tlc_if.c` 等
  - `vos/`：VOS (Virtual Operating System) 抽象層，提供 `posix/`、`windows/`、`vxworks/` 等平台實作
- `example/`：範例程式，示範 PD 與 MD 的傳送與接收，如 `sendHello.c`、`receiveHello.c`
- `test/`：測試程式與腳本，涵蓋多種通訊與 XML 功能測試
- `ladder/`：與 Ladder 技術相關的支援檔案與 Makefile
- `spy/`：Wireshark 外掛，協助解析 TRDP 封包
- `doc/`：PDF 規格與參考手冊

## 建置流程

1. 選擇目標平台設定 (僅需執行一次)：
   ```bash
   make LINUX_X86_64_config    # 例如：64 位元 Linux
   ```
2. 編譯目標：
   ```bash
   make            # 建置主要函式庫與工具
   make example    # 建置範例程式
   make test       # 建置測試工具
   ```
   其他可用目標與選項可透過 `make help` 查看。

## 執行範例

1. 完成建置後，執行範例程式位於 `bld/output/<arch>-rel/` 或 `bld/output/<arch>-dbg/`。
2. 在兩個終端機中分別執行接收與傳送範例：
   ```bash
   ./bld/output/<arch>-rel/receiveHello -o <本機IP>
   ./bld/output/<arch>-rel/sendHello -o <本機IP> -t <目標IP>
   ```
   程式會在主控台顯示傳送或接收的資料。

## 測試與其他工具

- `make test` 會建置測試伺服器程式；`test/` 目錄提供多種測試模式
- `spy/` 目錄內的外掛可編譯成 Wireshark 模組，協助檢視 TRDP 封包
- 使用 `make doc` 可產生 API 參考手冊 (需安裝 doxygen)

## Python 使用

TCNOpen3 本體以 C 語言撰寫，但建置後會產生動態函式庫，
可透過 Python 的 `ctypes` 呼叫 API。下列範例示範取得版本字串：

1. 建置函式庫：
   ```bash
   make LINUX_X86_64_config
   make
   ```
2. 在 Python 中載入並呼叫：
   ```python
   import ctypes

   lib = ctypes.CDLL('bld/output/linux_x86_64-rel/libtrdp.so')
   lib.tlc_getVersionString.restype = ctypes.c_char_p
   print(lib.tlc_getVersionString().decode())
   ```

`libtrdp.so` 的路徑與架構需與系統一致，更多函式可參考 `src/api/` 中的標頭檔。

## 進階選項

`make help` 會列出可用的建置選項，例如啟用高效能模式 (`HIGH_PERF_INDEXED`) 或即時排程 (`RT_THREADS`) 等。

