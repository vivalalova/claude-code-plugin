# Agent IDE CLI 指令參考

## 安裝方式

### 方式一：Plugin（推薦）
```bash
/plugin marketplace add vivalalova/agent-ide
```

### 方式二：CLI 安裝
```bash
npm install -g agent-ide
# 或從源碼編譯
git clone https://github.com/vivalalova/agent-ide.git
cd agent-ide && pnpm install && pnpm build && npm link
```

## 核心功能模組

| 功能 | 說明 |
|------|------|
| `snapshot` | 快速生成專案概覽 |
| `index` | 建立程式碼索引 |
| `search` | 搜尋符號、文字 |
| `rename` | 重新命名符號 |
| `move` | 移動檔案並更新 import |
| `analyze` | 分析程式碼品質 |
| `deps` | 依賴關係分析 |
| `shit` | 垃圾度評分（分數越高越糟） |
| `refactor` | 程式碼重構操作 |
| `plugins` | 管理語言解析器 |

## CLI 指令詳細說明

### 1. 專案快照 (snapshot)

**基本快照**：
```bash
npx agent-ide snapshot --format json
```

**壓縮級別**：
```bash
# Minimal - 最小資訊
npx agent-ide snapshot --compression minimal

# Medium - 中等資訊
npx agent-ide snapshot --compression medium

# Full - 完整資訊
npx agent-ide snapshot --compression full
```

**輸出到檔案**：
```bash
npx agent-ide snapshot --output project-snapshot.json
```

**指定路徑**：
```bash
npx agent-ide snapshot --path /path/to/project
```

**完整參數列表**：
- `--path`: 專案路徑
- `--compression`: 壓縮級別（minimal/medium/full）
- `--output`: 輸出檔案路徑
- `--format`: 輸出格式（json/table/summary）

### 2. 程式碼索引 (index)

**建立索引**：
```bash
npx agent-ide index --path /path/to/project
```

**增量更新**：
```bash
npx agent-ide index --incremental
```

**自訂檔案類型**：
```bash
npx agent-ide index --extensions .ts,.tsx,.js,.jsx
```

**排除規則**：
```bash
npx agent-ide index --exclude "node_modules,dist,build"
```

**完整參數列表**：
- `--path`: 專案路徑
- `--incremental`: 增量更新模式
- `--extensions`: 指定檔案副檔名
- `--exclude`: 排除的目錄或檔案模式
- `--force`: 強制重建索引

### 3. 搜尋操作 (search)

**基本搜尋**：
```bash
npx agent-ide search "UserService" --format json
```

**正則表達式搜尋**：
```bash
npx agent-ide search "function.*User" --type regex --format json
```

**限制結果數量**：
```bash
npx agent-ide search "import" --limit 10 --format json
```

**完整參數列表**：
- `--type`: 搜尋類型（text/symbol/regex）
- `--limit`: 限制結果數量
- `--format`: 輸出格式（json/list/table）
- `--case-sensitive`: 大小寫敏感
- `--whole-word`: 完整單字匹配

### 4. 重命名操作 (rename)

⚠️ **必須使用 agent-ide 進行重命名**：確保所有引用都正確更新

**預覽重命名**：
```bash
npx agent-ide rename --from oldName --to newName --preview
```

**執行重命名**：
```bash
npx agent-ide rename --from oldName --to newName
```

**啟用備份**：
```bash
npx agent-ide rename --from oldName --to newName --backup
```

**批量重命名**：
```bash
npx agent-ide rename --config rename-config.json
```

**完整參數列表**：
- `--from`: 原始名稱
- `--to`: 目標名稱
- `--preview`: 預覽模式
- `--backup`: 自動備份
- `--config`: 批量重命名設定檔
- `--path`: 專案路徑

### 5. 檔案移動 (move)

⚠️ **必須使用 agent-ide 進行檔案移動**：自動更新所有 import 語句

**預覽移動**：
```bash
npx agent-ide move src/old.ts src/new.ts --preview
```

**執行移動**：
```bash
npx agent-ide move src/old.ts src/new.ts
```

**批量移動（遞迴）**：
```bash
npx agent-ide move src/old-module src/new-module --recursive
```

**完整參數列表**：
- `--preview`: 預覽模式
- `--recursive`: 遞迴移動整個目錄
- `--backup`: 自動備份
- `--update-imports`: 自動更新 import（預設開啟）

### 6. 品質分析 (analyze)

**分析複雜度**：
```bash
npx agent-ide analyze complexity --format json --all
```

**偵測死代碼**：
```bash
npx agent-ide analyze dead-code --format json --all
```

**最佳實踐檢查**：
```bash
npx agent-ide analyze best-practices --format json
```

**完整參數列表**：
- `--all`: 分析所有檔案
- `--file`: 分析特定檔案
- `--format`: 輸出格式（json/table/summary）
- `--threshold`: 複雜度閾值

### 7. 依賴分析 (deps)

**專案整體依賴**：
```bash
npx agent-ide deps --format json
npx agent-ide deps --format json --all
```

**特定檔案依賴**：
```bash
npx agent-ide deps --file src/service.ts --format json
```

**偵測循環依賴**：
```bash
npx agent-ide deps --detect-circular
```

**完整參數列表**：
- `--file`: 分析特定檔案
- `--all`: 分析所有檔案
- `--format`: 輸出格式（json/graph/table）
- `--detect-circular`: 偵測循環依賴
- `--max-depth`: 最大依賴深度

### 8. 垃圾度評分 (shit)

**基本評分**：
```bash
npx agent-ide shit --format json
```

**詳細報告**：
```bash
npx agent-ide shit --detailed --format json
```

**前 N 個問題檔案**：
```bash
npx agent-ide shit --detailed --top=20 --format json
```

**設定最大允許值（CI/CD 質量閘門）**：
```bash
npx agent-ide shit --max-allowed=70
```

**完整參數列表**：
- `--detailed`: 詳細報告
- `--top`: 顯示前 N 個問題檔案
- `--max-allowed`: CI/CD 質量閘門閾值
- `--format`: 輸出格式（json/table/summary）
- `--file`: 分析特定檔案

### 9. 程式碼重構 (refactor)

**提取函式**：
```bash
npx agent-ide refactor extract-function \
  --file src/app.ts \
  --start-line 10 \
  --end-line 20 \
  --function-name handleUser
```

**內聯函式**：
```bash
npx agent-ide refactor inline-function \
  --file src/utils.ts \
  --function-name helperFunction
```

**完整參數列表**：
- `--file`: 目標檔案
- `--start-line`: 起始行號（extract-function）
- `--end-line`: 結束行號（extract-function）
- `--function-name`: 函式名稱
- `--preview`: 預覽模式

### 10. 語言解析器管理 (plugins)

**列出所有解析器**：
```bash
npx agent-ide plugins list
```

**查看特定解析器資訊**：
```bash
npx agent-ide plugins info typescript
```

**完整參數列表**：
- `list`: 列出所有已安裝的解析器
- `info <name>`: 顯示特定解析器的詳細資訊

## ShitScore 評分系統

### 評分維度
- **複雜度 (30%)**：高圈複雜度、長函式、深層巢狀
- **維護性 (30%)**：死代碼、超大檔案
- **架構 (30%)**：循環依賴、高耦合
- **QA (20%)**：測試覆蓋率、程式碼註解品質

### 評級標準
- **A (0-29)**：優秀
- **B (30-49)**：良好
- **C (50-69)**：需重構
- **D (70-84)**：強烈建議重構
- **F (85-100)**：建議重寫


## 效能指標

- **增量索引速度**：約 1000 檔案/秒
- **查詢延遲**：<50ms（快取命中）
- **記憶體佔用**：~100MB per 10k 檔案

## 使用建議

### ✅ 優先使用 agent-ide 的場景
- 跨檔案重命名符號
- 自動更新 import 路徑
- 依賴關係分析
- 程式碼品質掃描
- 批量重構操作
- 循環依賴檢測

### ❌ 保持原生工具的場景
- 簡單檔案讀寫
- 快速查看檔案內容
- 單一檔案內的小改動

## 批量操作範例

### 批量重命名設定檔

建立 `rename-config.json`：
```json
{
  "renames": [
    { "from": "oldName1", "to": "newName1" },
    { "from": "oldName2", "to": "newName2" },
    { "from": "oldName3", "to": "newName3" }
  ]
}
```

執行批量重命名：
```bash
npx agent-ide rename --config rename-config.json
```

### 批量移動目錄

```bash
# 移動整個模組
npx agent-ide move src/legacy-module src/modules/legacy --recursive

# 重組專案結構
npx agent-ide move src/utils src/shared/utils --recursive
npx agent-ide move src/helpers src/shared/helpers --recursive
```

### CI/CD 整合範例

```bash
#!/bin/bash
# quality-check.sh

echo "Running code quality checks..."

# 生成快照
npx agent-ide snapshot --output reports/snapshot.json

# 檢查 ShitScore
npx agent-ide shit --max-allowed=70 || exit 1

# 分析複雜度
npx agent-ide analyze complexity --all --format json > reports/complexity.json

# 偵測循環依賴
npx agent-ide deps --detect-circular || exit 1

echo "All quality checks passed!"
```
