# Agent IDE 使用範例

## 範例 1：重命名符號

### 使用者需求
「將 `getUserData` 重命名為 `fetchUserProfile` 並更新所有引用」

### 執行步驟

1. **預覽變更**：
```bash
npx agent-ide rename --from getUserData --to fetchUserProfile --preview
```

2. **確認並執行**：
```bash
npx agent-ide rename --from getUserData --to fetchUserProfile
```

### 預期結果
- 所有檔案中的 `getUserData` 都被重命名為 `fetchUserProfile`
- 所有 import 語句自動更新
- 所有函式呼叫自動更新
- 所有註解中的引用保持不變（需手動檢查）

## 範例 2：移動檔案並更新 Import

### 使用者需求
「將 `src/utils/helpers.ts` 移動到 `src/shared/utils/helpers.ts`」

### 執行步驟

1. **確保目標目錄存在**：
```bash
mkdir -p src/shared/utils
```

2. **預覽移動**：
```bash
npx agent-ide move src/utils/helpers.ts src/shared/utils/helpers.ts --preview
```

3. **執行移動**：
```bash
npx agent-ide move src/utils/helpers.ts src/shared/utils/helpers.ts
```

### 預期結果
- 檔案成功移動到新位置
- 所有 import 語句自動更新：
  - 舊：`import { helper } from '../utils/helpers'`
  - 新：`import { helper } from '../shared/utils/helpers'`

## 範例 3：分析專案品質

### 使用者需求
「分析專案中的循環依賴和程式碼品質問題」

### 執行步驟

1. **執行垃圾度評分**：
```bash
npx agent-ide shit --detailed --format json
```

2. **分析複雜度**：
```bash
npx agent-ide analyze complexity --format json --all
```

3. **檢查依賴關係**：
```bash
npx agent-ide deps --format json --all
```

4. **偵測死代碼**：
```bash
npx agent-ide analyze dead-code --format json --all
```

### 預期結果
- ShitScore 報告顯示整體品質評級
- 複雜度分析列出高複雜度函式
- 依賴關係圖顯示循環依賴
- 死代碼列表顯示未使用的程式碼

## 範例 4：重構長函式

### 使用者需求
「將 `src/app.ts` 中第 50-80 行的邏輯提取為 `processUserData` 函式」

### 執行步驟

1. **提取函式**：
```bash
npx agent-ide refactor extract-function \
  --file src/app.ts \
  --start-line 50 \
  --end-line 80 \
  --function-name processUserData
```

2. **驗證結果**：
- 檢查新函式是否正確建立
- 確認原位置已替換為函式呼叫
- 測試功能是否正常

### 預期結果
- 新函式 `processUserData` 建立在適當位置
- 原程式碼被函式呼叫取代
- 所有變數作用域正確處理

## 範例 5：搜尋符號並分析使用

### 使用者需求
「找出專案中所有使用 `UserService` 的地方」

### 執行步驟

1. **搜尋符號**：
```bash
npx agent-ide search "UserService" --format json
```

2. **分析特定檔案的依賴**：
```bash
npx agent-ide deps --file src/services/user-service.ts --format json
```

### 預期結果
- 列出所有引用 `UserService` 的檔案和位置
- 顯示 `UserService` 的依賴關係
- 幫助理解 `UserService` 的使用模式

## 範例 6：批量重構工作流程

### 使用者需求
「重構 `auth` 模組：重命名主類別、移動檔案、分析影響範圍」

### 執行步驟

1. **分析當前狀態**：
```bash
npx agent-ide deps --file src/auth/AuthService.ts --format json
npx agent-ide search "AuthService" --format json
```

2. **重命名類別**：
```bash
npx agent-ide rename --from AuthService --to AuthenticationService --preview
npx agent-ide rename --from AuthService --to AuthenticationService
```

3. **重組檔案結構**：
```bash
mkdir -p src/modules/authentication
npx agent-ide move src/auth/AuthenticationService.ts src/modules/authentication/service.ts
```

4. **驗證品質**：
```bash
npx agent-ide shit --detailed --format json
npx agent-ide analyze complexity --format json
```

### 預期結果
- 所有重命名正確執行
- Import 路徑全部更新
- 專案結構更清晰
- 品質評分改善

## 範例 7：處理循環依賴

### 使用者需求
「偵測並解決專案中的循環依賴問題」

### 執行步驟

1. **偵測循環依賴**：
```bash
npx agent-ide deps --format json --all
```

2. **分析問題檔案**：
```bash
npx agent-ide deps --file src/services/user.ts --format json
npx agent-ide deps --file src/services/auth.ts --format json
```

3. **重構解決方案**：
- 識別循環依賴的原因
- 提取共用介面到獨立檔案
- 使用依賴注入打破循環

4. **驗證修復**：
```bash
npx agent-ide deps --format json --all
npx agent-ide shit --format json
```

### 預期結果
- 循環依賴被識別
- 透過重構解決循環依賴
- ShitScore 中的「架構」評分降低

## 範例 8：使用 snapshot 快速診斷專案

### 使用者需求
「快速了解新接手的專案結構和品質狀況」

### 執行步驟

1. **生成完整快照**：
```bash
npx agent-ide snapshot --compression full --output project-snapshot.json
```

2. **查看快照結果**：
- 檔案數量和結構
- 程式碼行數統計
- 基本品質指標
- 主要問題區域

3. **針對性分析**：
```bash
# 查看詳細品質報告
npx agent-ide shit --detailed --top=20

# 分析高複雜度檔案
npx agent-ide analyze complexity --all
```

### 預期結果
- 8.7 秒內完成 62 檔案專案分析
- 消耗約 59,138 tokens
- 快速識別問題而無需手動檢查檔案
- 建立改善路線圖

### 實際案例

**專案規模**：62 檔案，4,740 行程式碼

**快照發現**：
- 46 個 `any` 型別使用
- ShitScore 22.02
- 3 個循環依賴
- 12 個高複雜度函式

**改善成效**：
- 消除所有 `any` 型別
- ShitScore 降至 19.56（-11%）
- 型別安全提升 35%

## 範例 9：CI/CD 整合與質量閘門

### 使用者需求
「在 CI/CD 流程中設定程式碼品質閘門，確保品質不降低」

### GitHub Actions 整合

建立 `.github/workflows/quality-check.yml`：

```yaml
name: Code Quality Check

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  quality:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install agent-ide
        run: npm install -g agent-ide

      - name: Generate snapshot
        run: npx agent-ide snapshot --output reports/snapshot.json

      - name: Check ShitScore (質量閘門)
        run: npx agent-ide shit --max-allowed=70

      - name: Analyze complexity
        run: npx agent-ide analyze complexity --all --format json

      - name: Detect circular dependencies
        run: npx agent-ide deps --detect-circular

      - name: Upload reports
        uses: actions/upload-artifact@v3
        with:
          name: quality-reports
          path: reports/
```

### 執行步驟

1. **設定質量閘門**：
   - 嚴格專案：`--max-allowed=50`（B 級或更好）
   - 一般專案：`--max-allowed=70`（C 級或更好）
   - 遺留專案：`--max-allowed=85`（避免 F 級）

2. **定期品質追蹤**：
```bash
#!/bin/bash
# 每日執行
DATE=$(date +%Y%m%d)
npx agent-ide snapshot --output "snapshots/$DATE.json"
npx agent-ide shit --format json > "quality/$DATE.json"
```

3. **品質趨勢分析**：
- 比對每日 ShitScore 變化
- 追蹤複雜度趨勢
- 監控依賴關係變化

### 預期結果
- CI/CD 自動檢查程式碼品質
- 品質不符標準時阻止合併
- 長期追蹤品質趨勢
- 團隊品質意識提升

## 範例 10：批量重構整個模組

### 使用者需求
「重構整個 `legacy` 模組，包含批量重命名、檔案重組和品質改善」

### 執行步驟

#### Phase 1：診斷分析

```bash
# 生成快照
npx agent-ide snapshot --compression full

# 評估當前品質
npx agent-ide shit --detailed --top=20

# 分析 legacy 模組依賴
npx agent-ide deps --file src/legacy/ --format json
```

**診斷結果範例**：
- ShitScore: 67.3（D 級）
- 直接影響 23 個檔案
- 間接影響 45 個檔案
- 3 個循環依賴

#### Phase 2：批量重命名

建立 `rename-config.json`：
```json
{
  "renames": [
    { "from": "getLegacyData", "to": "fetchData" },
    { "from": "oldProcessor", "to": "dataProcessor" },
    { "from": "legacyHandler", "to": "requestHandler" }
  ]
}
```

執行批量重命名：
```bash
npx agent-ide rename --config rename-config.json --preview
npx agent-ide rename --config rename-config.json
```

#### Phase 3：檔案重組

```bash
# 建立新結構
mkdir -p src/modules/data-processing

# 批量移動檔案
npx agent-ide move src/legacy/processor src/modules/data-processing --recursive --preview
npx agent-ide move src/legacy/processor src/modules/data-processing --recursive
```

#### Phase 4：降低複雜度

```bash
# 找出高複雜度函式
npx agent-ide analyze complexity --all

# 提取複雜邏輯
npx agent-ide refactor extract-function \
  --file src/modules/data-processing/processor.ts \
  --start-line 45 \
  --end-line 120 \
  --function-name processData
```

#### Phase 5：驗證改善

```bash
# 執行測試
npm test

# 驗證品質改善
npx agent-ide shit --detailed

# 檢查循環依賴
npx agent-ide deps --detect-circular
```

### 預期結果

**品質改善**：
- ShitScore 從 67.3 降到 35.8（-47%）
- 複雜度從 45 降到 5
- 消除所有循環依賴
- 檔案結構更清晰

**影響範圍**：
- 直接修改 23 個檔案
- 自動更新 45 個檔案的 import
- 零 TypeScript 編譯錯誤
- 所有測試通過

**團隊收益**：
- 維護成本降低 40%
- 新功能開發速度提升 25%
- Bug 率降低 30%

## 最佳實踐

### 重構前必做
1. 執行 `--preview` 檢查影響範圍
2. 確保有版本控制（git commit）
3. 執行測試確保功能正常

### 重構後必做
1. 執行 `npx agent-ide shit` 確認品質改善
2. 執行專案的 build 和 lint
3. 執行測試套件
4. Commit 變更

### 效率技巧
1. 使用 `--format json` 方便程式化處理
2. 善用 `--preview` 避免錯誤
3. 搭配 `code_deps` 和 `code_search` 理解影響範圍
4. 定期執行 `code_shit` 追蹤品質趨勢
