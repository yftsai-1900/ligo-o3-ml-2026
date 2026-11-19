# AGENTS.md — AI 程式助手專案規則

本檔案是這個專案的共同規則。所有 AI 程式助手（Claude Code、Cursor、GitHub Copilot、Codex 等）在讀取或修改程式碼前，都必須遵守以下規則。

## 專案概述

- 課程專題：用機器學習研究 LIGO 第三次觀測期（O3）的重力波數據。
- O3 分為 O3a（2019-04-01 至 2019-10-01）與 O3b（2019-11-01 至 2020-03-27）。
- 偵測器代號：H1（Hanford）、L1（Livingston）、V1（Virgo）。
- 公開資料來源：GWOSC（https://gwosc.org）。

## 環境管理

- 一律使用 **uv**。不要使用 `pip install`、conda，也不要建立 `requirements.txt`。
- 執行程式一律透過 uv：
  - `uv run python scripts/xxx.py`
  - `uv run pytest`
  - `uv run jupyter lab`
- 新增套件：`uv add 套件名`；新增開發工具：`uv add --dev 套件名`。
- **新增套件前，先向使用者說明需要的理由，經確認後才執行。** 不要擅自加入套件。
- 不要手動編輯 `uv.lock`。
- Python 版本固定為 3.11（見 `pyproject.toml`），不要修改。

## 專案結構

```
src/ligo_o3_ml/   共用函式庫（資料讀取、前處理、模型、評估）
scripts/          可直接執行的腳本（下載資料、訓練、評估）
notebooks/        探索與畫圖用的 Jupyter notebook
tests/            pytest 測試
data/             原始與處理後資料（不進 Git）
models/           訓練好的模型與 checkpoint（不進 Git）
```

## 共用函式庫規則

- 寫新函式前，**先搜尋 `src/ligo_o3_ml/` 是否已有類似功能**，有就沿用或擴充，不要重寫一份。
- 會被兩個以上檔案使用的函式，一律放進 `src/ligo_o3_ml/`，用 `from ligo_o3_ml.xxx import yyy` 匯入。
- notebook 只用來探索與畫圖；穩定的程式碼要移進 `src/ligo_o3_ml/`。
- 修改共用函式的參數或回傳值時，要一併修改所有呼叫處，並在回覆中說明影響範圍。

## 檔案與機密

- 不要 commit `data/`、`models/` 或任何資料檔（`.hdf5`、`.h5`、`.gwf`）與模型檔（`.ckpt`、`.pt`、`.pth`）。
- 不要讀取、印出或修改 `.env`。不要在程式碼中寫死 API 金鑰。
- 不要寫死個人電腦的絕對路徑（例如 `C:\Users\...`、`/Users/...`）。路徑一律以專案根目錄為基準，用 `pathlib` 組合。

## 程式風格

- 函式要有 type hints 與 docstring，並寫明物理量的單位（秒、Hz、GPS 時間等）。
- 有隨機性的地方（資料切分、模型初始化、訓練）都要設定 seed，確保結果可重現。
- 完成修改後依序執行：
  1. `uv run ruff format .`
  2. `uv run ruff check . --fix`
  3. `uv run pytest`
- 新增共用函式時，盡量在 `tests/` 補上對應的簡單測試。

## 資料處理注意事項

- 時間一律使用 GPS 秒。
- GWOSC 提供 4096 Hz 與 16384 Hz 兩種取樣率；下載時要明確指定，同一個實驗內不要混用。
- 只使用資料品質良好的時段（參考 GWOSC 的 data quality / science segments）。
- 切分訓練集與測試集時，以「事件」或「時間段」為單位分組，避免同一事件的片段同時出現在訓練集與測試集（資料洩漏）。

## Git 工作流程

- 不要直接 commit 到 `main`，在功能分支上工作（例如 `feature/whitening`）。
- 每次修改範圍小而集中，一個分支只做一件事。
- 不要執行 `git push --force`、`git reset --hard` 等破壞性指令。
- commit 訊息簡短說明改了什麼，中文或英文皆可。
