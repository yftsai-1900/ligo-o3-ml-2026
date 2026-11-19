# ligo-o3-ml

以機器學習研究 LIGO 第三次觀測期（O3）重力波數據的課程專題。

## 成員

| 姓名 | GitHub 帳號 | 負責項目 |
| --- | --- | --- |
| （待填） | （待填） | （待填） |
| （待填） | （待填） | （待填） |
| （待填） | （待填） | （待填） |

## 第一次安裝

1. **安裝 uv**

   - macOS / Linux：
     ```
     curl -LsSf https://astral.sh/uv/install.sh | sh
     ```
   - Windows（PowerShell）：
     ```
     powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
     ```

2. **下載專案**：用 GitHub Desktop（File → Clone repository）或 VS Code 把專案 clone 到電腦。

3. **建立環境**：在專案資料夾開啟終端機，執行：
   ```
   uv sync
   ```
   uv 會自動下載 Python 3.11，並安裝與其他成員完全相同版本的套件。

4. **設定 notebook 自動清除輸出**（每人執行一次）：
   ```
   uv run nbstripout --install
   ```

5. **確認環境正常**：
   ```
   uv run pytest
   ```
   看到測試通過就代表安裝成功。

## 日常使用

| 要做的事 | 指令 |
| --- | --- |
| 從 GitHub 拉下最新版本後同步環境 | `uv sync` |
| 執行 Python 腳本 | `uv run python scripts/xxx.py` |
| 開啟 Jupyter | `uv run jupyter lab` |
| 新增套件（先和組員討論） | `uv add 套件名` |
| 排版與檢查程式碼 | `uv run ruff format .` 和 `uv run ruff check .` |
| 執行測試 | `uv run pytest` |

新增套件後，`pyproject.toml` 和 `uv.lock` 會一起改變，兩個檔案都要 commit。

## 下載資料

資料不放進 Git，請各自從 GWOSC 下載到 `data/` 資料夾。範例：

```python
from gwosc.datasets import event_gps
from gwpy.timeseries import TimeSeries

gps = event_gps("GW190521")  # O3a 的事件
strain = TimeSeries.fetch_open_data("H1", gps - 16, gps + 16, sample_rate=4096)
strain.write("data/GW190521_H1_4096.hdf5")
```

## 專案結構

```
ligo-o3-ml/
├── pyproject.toml      套件清單與工具設定
├── uv.lock             鎖定的套件版本（不要手動修改）
├── AGENTS.md           AI 程式助手規則
├── CLAUDE.md           Claude Code 讀取的設定（指向 AGENTS.md）
├── src/ligo_o3_ml/     共用函式庫
├── scripts/            可執行的腳本
├── notebooks/          探索用 notebook
├── tests/              測試
├── data/               資料（不進 Git）
└── models/             模型（不進 Git）
```

共用函式放在 `src/ligo_o3_ml/`，在任何地方都可以這樣匯入：

```python
from ligo_o3_ml.data import load_strain
```

## 協作規則

1. 開工前先 **Pull**，再執行 `uv sync`。
2. 每做一件事就開一個新分支，例如 `feature/whitening`。
3. 完成後 push 並開 Pull Request，請另一位組員看過再合併到 `main`。
4. 不要直接修改 `main`。
5. 不要 commit `.env`、資料檔或模型檔。

## 使用 AI 程式助手

所有 AI 助手都要遵守 [AGENTS.md](AGENTS.md) 的規則。Cursor、Copilot、Codex 會自動讀取這個檔案，Claude Code 則透過 `CLAUDE.md` 讀取。
