---
name: agent-ide
description: Code refactoring and analysis toolkit using agent-ide CLI. Use this skill when the user needs to: (1) Analyze project structure and code quality with snapshots, (2) Rename symbols (classes, functions, variables) across files, (3) Move files and automatically update all import statements, (4) Detect circular dependencies and analyze dependency relationships, (5) Calculate ShitScore and identify code quality issues, (6) Refactor complex code (extract functions, reduce complexity). Supports TypeScript and JavaScript projects. Keywords: refactor, rename, move, analyze, quality, dependencies, snapshot, restructure, code smell, technical debt.
---

# Agent IDE

## 概述

Agent IDE 是為 AI 代理設計的程式碼智能工具集，提供索引、搜尋、重構、依賴分析等功能。此 skill 讓 Claude 能夠安全且高效地執行跨檔案重構操作，確保所有引用和 import 語句正確更新。

## 何時使用此 Skill

使用 agent-ide skill 於以下場景：

- **重構操作**：需要重命名類別、函式、變數等符號
- **檔案重組**：需要移動檔案並自動更新所有 import 路徑
- **品質分析**：需要分析程式碼複雜度、偵測死代碼、評估整體品質
- **依賴管理**：需要分析依賴關係、偵測循環依賴
- **大規模改動**：需要跨多個檔案進行一致性變更
- **架構優化**：需要重組專案結構並保持引用完整性

**不使用此 skill 的場景**：
- 簡單的檔案讀寫操作
- 單一檔案內的小改動
- 快速查看檔案內容

## 核心功能

### 1. 專案快照 (snapshot)

**用途**：快速生成專案概覽，無需讀取所有檔案即可了解專案結構、檔案數量和品質指標。

**壓縮級別**：

**Minimal**：最小資訊，僅包含基本統計
```bash
npx agent-ide snapshot --compression minimal --format json
```

**Medium**：中等資訊，包含檔案列表和基本指標
```bash
npx agent-ide snapshot --compression medium --format json
```

**Full**：完整資訊，包含詳細的品質分析
```bash
npx agent-ide snapshot --compression full --format json
```

**輸出到檔案**：
```bash
npx agent-ide snapshot --output project-snapshot.json
```

**效能特性**：
- 62 檔案專案約 8.7 秒完成分析
- 消耗約 59,138 tokens
- 快速識別問題而無需手動檢查檔案

**適用場景**：
- 初次接觸專案，快速了解結構
- 大型專案（>50 檔案）的品質評估
- CI/CD 中的定期品質快照
- 重構前後的對比分析

詳細範例參考 `references/examples.md` 的「範例 8：使用 snapshot 快速診斷專案」。

### 2. 程式碼索引 (index)

**用途**：建立可搜尋的程式碼索引，支援增量更新和自訂排除規則。

**建立索引**：
```bash
npx agent-ide index --path /path/to/project
```

**增量更新**：
```bash
npx agent-ide index --path /path/to/project --incremental
```

**自訂檔案類型**：
```bash
npx agent-ide index --extensions .ts,.tsx,.js,.jsx
```

**排除規則**：
```bash
npx agent-ide index --exclude "node_modules,dist,build"
```

**效能指標**：
- 索引速度：約 1000 檔案/秒
- 查詢延遲：<50ms（快取命中）
- 記憶體佔用：約 100MB per 10k 檔案

**注意事項**：
- 首次索引需要完整掃描
- 後續使用增量更新提升效率
- 索引結果用於 search、rename、move 等操作

### 3. 符號重命名 (rename)

**用途**：重命名類別、函式、變數等符號，並自動更新所有引用。

**必要步驟**：

1. 使用 `--preview` 預覽變更影響範圍
2. 確認變更範圍合理後執行重命名
3. 驗證重命名結果

**範例指令**：
```bash
# 預覽
npx agent-ide rename --from oldName --to newName --preview

# 執行
npx agent-ide rename --from oldName --to newName
```

**注意事項**：
- 必須使用 agent-ide 進行重命名，不可手動修改以避免遺漏引用
- 重命名前確保專案已 commit，方便回滾
- 註解中的引用不會自動更新，需手動檢查

詳細範例參考 `references/examples.md` 的「範例 1：重命名符號」。

### 2. 檔案移動 (code_move)

**用途**：移動檔案到新位置，並自動更新所有 import 語句。

**必要步驟**：

1. 確保目標目錄已存在
2. 使用 `--preview` 預覽 import 變更
3. 執行移動操作
4. 驗證 import 路徑正確性

**範例指令**：
```bash
# 建立目標目錄
mkdir -p src/shared/utils

# 預覽
npx agent-ide move src/old.ts src/new.ts --preview

# 執行
npx agent-ide move src/old.ts src/new.ts
```

**注意事項**：
- 必須使用 agent-ide 進行檔案移動，確保 import 路徑自動更新
- 移動前確認沒有同名檔案衝突
- 驗證相對路徑和絕對路徑都正確更新

詳細範例參考 `references/examples.md` 的「範例 2：移動檔案並更新 Import」。

### 3. 品質分析 (code_analyze & code_shit)

**用途**：分析程式碼品質、複雜度、死代碼，並產生垃圾度評分。

**分析類型**：

**複雜度分析**：
```bash
npx agent-ide analyze complexity --format json --all
```

**死代碼偵測**：
```bash
npx agent-ide analyze dead-code --format json --all
```

**最佳實踐檢查**：
```bash
npx agent-ide analyze best-practices --format json
```

**垃圾度評分**：
```bash
# 基本評分
npx agent-ide shit --format json

# 詳細報告
npx agent-ide shit --detailed --format json

# 前 N 個問題檔案
npx agent-ide shit --detailed --top=20 --format json
```

**ShitScore 評級標準**：
- **A (0-29)**：優秀
- **B (30-49)**：良好
- **C (50-69)**：需重構
- **D (70-84)**：強烈建議重構
- **F (85-100)**：建議重寫

**評分維度**：
- 複雜度 (30%)：高圈複雜度、長函式、深層巢狀
- 維護性 (30%)：死代碼、超大檔案
- 架構 (30%)：循環依賴、高耦合
- QA (20%)：測試覆蓋率、程式碼註解品質

詳細範例參考 `references/examples.md` 的「範例 3：分析專案品質」。

### 4. 依賴分析 (code_deps)

**用途**：分析專案依賴關係、偵測循環依賴、理解模組間的耦合關係。

**分析範圍**：

**專案整體依賴**：
```bash
npx agent-ide deps --format json
npx agent-ide deps --format json --all
```

**特定檔案依賴**：
```bash
npx agent-ide deps --file src/service.ts --format json
```

**應用場景**：
- 重構前了解影響範圍
- 偵測循環依賴問題
- 分析模組耦合度
- 規劃架構優化方向

詳細範例參考 `references/examples.md` 的「範例 7：處理循環依賴」。

### 5. 程式碼搜尋 (code_search)

**用途**：搜尋符號、文字、或使用正則表達式搜尋程式碼。

**搜尋模式**：

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

詳細範例參考 `references/examples.md` 的「範例 5：搜尋符號並分析使用」。

### 6. 程式碼重構 (refactor)

**用途**：執行結構化重構操作，如提取函式或內聯函式。

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

**支援的重構類型**：
- extract-function：將程式碼區塊提取為獨立函式
- inline-function：將函式內容內聯到呼叫處
- 支援指定行號範圍精確重構

詳細範例參考 `references/examples.md` 的「範例 4：重構長函式」。

### 7. 語言解析器管理 (plugins)

**用途**：管理支援的程式語言解析器，查看已安裝的語言支援。

**列出所有解析器**：
```bash
npx agent-ide plugins list
```

**查看特定解析器資訊**：
```bash
npx agent-ide plugins info typescript
```

**支援的語言**：
- TypeScript / JavaScript
- 未來可擴展其他語言支援

## 工作流程哲學

Agent IDE 強調**「Preview → Confirm → Execute → Verify」**的工作方法論，確保所有重構操作安全、可追蹤且可測量。

### Preview（預覽）
- 所有重構操作必須先使用 `--preview` 參數
- 評估影響範圍：直接影響的檔案 vs 間接影響的檔案
- 識別潛在風險：命名衝突、循環依賴、型別錯誤

### Confirm（確認）
- 審查預覽結果，確認變更符合預期
- 檢查是否有意外的副作用
- 必要時調整重構策略

### Execute（執行）
- 執行實際的程式碼變更
- 變更必須是原子操作：全部成功或全部回退
- 搭配版本控制系統（Git）確保可回溯

### Verify（驗證）
- 執行 build 和 lint 確保語法正確
- 執行測試套件確保功能完整
- 執行品質分析確認改善成效
- Commit 變更並記錄改善指標

**核心原則**：
- **禁止手動逐一修改**：變數/函式/類別改名時必須使用 agent-ide
- **小步迭代**：每次改動後立即驗證，避免累積風險
- **數據驅動**：追蹤 ShitScore 等品質指標，量化改善成果

## 五個核心工作流程

### 1. 專案診斷（Project Diagnosis）

**目標**：系統性評估專案的所有問題，建立改善路線圖。

**步驟**：
1. 使用 `snapshot` 快速了解專案規模和結構
2. 使用 `shit --detailed` 評估整體品質
3. 使用 `analyze complexity` 識別高複雜度程式碼
4. 使用 `analyze dead-code` 找出未使用的程式碼
5. 使用 `deps` 偵測循環依賴和孤立檔案
6. 建立優先級清單和改善計畫

**範例**：
```bash
npx agent-ide snapshot --compression full
npx agent-ide shit --detailed --top=20
npx agent-ide analyze complexity --all
npx agent-ide deps --format json
```

### 2. 安全重構（Safe Refactoring）

**目標**：執行跨檔案重構，確保所有引用正確更新。

**步驟**：
1. **Preview**：使用 `--preview` 評估影響範圍
2. **Confirm**：審查變更列表，確認符合預期
3. **Backup**：確保 Git 狀態乾淨，必要時建立分支
4. **Execute**：執行重命名或移動操作
5. **Verify**：執行 build、lint、test 確保無誤
6. **Measure**：比對重構前後的 ShitScore

**範例**：
```bash
# Preview
npx agent-ide rename --from oldName --to newName --preview

# Execute
npx agent-ide rename --from oldName --to newName

# Verify
npm run build && npm run lint && npm test
npx agent-ide shit --format json
```

### 3. 程式碼清理（Code Cleanup）

**目標**：系統性地移除死代碼，提升可維護性。

**步驟**：
1. 使用 `analyze dead-code` 識別未使用的程式碼
2. 使用 `search` 確認真的沒有引用
3. 使用 `deps` 確認沒有隱藏依賴
4. 分階段刪除死代碼
5. 執行測試確保無副作用
6. 追蹤維護性指標改善

**範例**：
```bash
npx agent-ide analyze dead-code --all
npx agent-ide search "unusedFunction"
npx agent-ide deps --file src/unused.ts
```

### 4. 複雜度降低（Complexity Reduction）

**目標**：將高複雜度函式拆解為更小、更易理解的單元。

**步驟**：
1. 使用 `analyze complexity` 找出高複雜度函式
2. 識別可提取的邏輯區塊
3. 使用 `refactor extract-function` 提取函式
4. 驗證功能完整性
5. 比對複雜度改善（例如：從 45 降到 5）

**範例**：
```bash
npx agent-ide analyze complexity
npx agent-ide refactor extract-function \
  --file src/complex.ts \
  --start-line 10 \
  --end-line 50 \
  --function-name extractedLogic
```

### 5. 全專案優化（Full Project Optimization）

**目標**：分階段系統性優化整個專案，大幅降低 ShitScore。

**步驟**：
1. **Phase 1 - 診斷**：snapshot + analyze 全面評估
2. **Phase 2 - 清理**：移除死代碼和重複邏輯
3. **Phase 3 - 重構**：降低複雜度和改善架構
4. **Phase 4 - 優化**：解決循環依賴和耦合問題
5. **Phase 5 - 驗證**：確認 ShitScore 大幅改善

**成效範例**：
- ShitScore 從 67.3 降到 35.8（-47%）
- 複雜度從 45 降到 5
- 消除 46 個 `any` 型別使用

## 工作流程指引

### 標準重構流程

執行重構操作時，遵循以下流程確保安全和品質：

1. **分析階段**：
   - 使用 `code_search` 了解符號使用情況
   - 使用 `code_deps` 分析依賴關係
   - 使用 `code_shit` 評估當前品質

2. **預覽階段**：
   - 使用 `--preview` 參數檢查變更影響
   - 確認變更範圍符合預期

3. **執行階段**：
   - 執行重命名或移動操作
   - 驗證結果正確性

4. **驗證階段**：
   - 執行專案的 build 和 lint
   - 執行測試套件
   - 再次執行 `code_shit` 確認品質改善
   - Commit 變更

### 批量重構範例

處理複雜重構任務時，按順序執行以下步驟：

1. 分析當前狀態（deps + search）
2. 執行重命名操作（rename）
3. 重組檔案結構（move）
4. 驗證品質改善（shit + analyze）

詳細範例參考 `references/examples.md` 的「範例 6：批量重構工作流程」。

## 最佳實踐

### 重構前必做
- 執行 `--preview` 檢查影響範圍
- 確保有版本控制（git commit）
- 執行測試確保功能正常

### 重構後必做
- 執行 `npx agent-ide shit` 確認品質改善
- 執行專案的 build 和 lint
- 執行測試套件
- Commit 變更

### 效率技巧
- 使用 `--format json` 方便程式化處理
- 善用 `--preview` 避免錯誤
- 搭配 `code_deps` 和 `code_search` 理解影響範圍
- 定期執行 `code_shit` 追蹤品質趨勢

## CI/CD 整合

### 質量閘門（Quality Gates）

使用 `--max-allowed` 設定 ShitScore 的最大允許值，超過閾值時 CI/CD 流程失敗。

**設定質量閘門**：
```bash
npx agent-ide shit --max-allowed=70 --format json
```

**評級建議**：
- **嚴格專案**：max-allowed=50（B 級或更好）
- **一般專案**：max-allowed=70（C 級或更好）
- **遺留專案**：max-allowed=85（避免 F 級）

### GitHub Actions 整合

```yaml
name: Code Quality Check
on: [push, pull_request]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install agent-ide
        run: npm install -g agent-ide
      - name: Check code quality
        run: npx agent-ide shit --max-allowed=70
      - name: Analyze complexity
        run: npx agent-ide analyze complexity --all
```

### 定期品質快照

```bash
# 每日執行，追蹤品質趨勢
npx agent-ide snapshot --output "snapshots/$(date +%Y%m%d).json"
npx agent-ide shit --format json > "quality/$(date +%Y%m%d).json"
```

## 進階安全特性

### 衝突檢測

Agent IDE 自動偵測以下衝突：

**命名衝突**：
- 重命名時檢查目標名稱是否已存在
- 防止覆蓋現有符號
- 提供衝突解決建議

**保留字衝突**：
- 防止使用語言保留字
- 檢查常見的全域變數名稱
- 避免與內建函式衝突

### 備份機制

**自動備份**：
```bash
npx agent-ide rename --from old --to new --backup
```

**Git 整合**：
- 變更前自動檢查 Git 狀態
- 建議在乾淨的工作目錄執行
- 支援自動 commit 和回退

### 批量操作

**批量重命名**：
```bash
# 使用設定檔批量重命名
npx agent-ide rename --config rename-config.json
```

**批量移動**：
```bash
# 移動整個目錄並更新所有引用
npx agent-ide move src/old-module src/new-module --recursive
```

### 影響範圍分析

所有操作提供詳細的影響範圍分析：

- **直接影響**：需要修改的檔案數量
- **間接影響**：依賴變更檔案的其他檔案
- **風險評估**：潛在的破壞性變更警告

**範例輸出**：
```
直接影響 12 個檔案
間接影響 34 個檔案
風險等級：中等
```

## 安裝與設定

完整的安裝指引和 CLI 指令參考請查閱 `references/cli-reference.md`。

**推薦安裝方式**：
```bash
/plugin marketplace add vivalalova/agent-ide
```

## 參考資源

此 skill 包含以下參考資源：

### references/cli-reference.md
詳細的 CLI 指令參考，包含：
- 完整的安裝方式
- 所有指令的參數說明
- ShitScore 評分系統詳解
- 程式化 API 範例
- 效能指標和使用建議

### references/examples.md
實際使用範例集，包含：
- 7 個完整的使用場景
- 每個場景的詳細執行步驟
- 預期結果說明
- 最佳實踐指引

載入這些參考資源以獲取更詳細的資訊和範例。
