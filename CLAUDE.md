# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 專案概述

這是 Vibe 團隊的 Claude Code Plugin Marketplace 專案，提供程式碼分析、重構和品質管理工具。

**Repository**: `https://github.com/vivalalova/claude-code-plugin`

### 包含的 Plugins

- **agent-ide**: 程式碼重構與分析專家工具集
  - 快速專案診斷（snapshot）
  - 符號重命名、檔案移動
  - 品質分析和 ShitScore 評分
  - 依賴關係分析和循環依賴檢測
  - CI/CD 整合支援

## 快速開始

### 安裝此 Marketplace

```bash
# 從 GitHub 安裝
/plugin marketplace add vivalalova/claude-code-plugin

# 或從本地路徑安裝（開發測試）
/plugin marketplace add /path/to/claude-code-plugin
```

### 安裝 agent-ide Plugin

```bash
/plugin install agent-ide@vibe-plugins
```

## 專案架構

### 檔案結構

```
.
├── .claude-plugin/
│   └── marketplace.json          # Marketplace 核心配置
├── plugins/
│   └── agent-ide/
│       ├── SKILL.md              # 主要說明文件
│       ├── references/
│       │   ├── cli-reference.md  # CLI 指令參考
│       │   └── examples.md       # 使用範例集
│       └── agent-ide.zip         # 打包的 plugin
├── CLAUDE.md                     # 本文件
└── .gitignore
```

### marketplace.json Schema

**必要欄位**：
- `name`: marketplace 識別符號 (kebab-case 格式)
- `owner`: 維護者資訊物件
- `plugins`: plugin 項目陣列

**選用欄位**：
- `metadata.description`: marketplace 簡述
- `metadata.version`: 版本追蹤
- `metadata.pluginRoot`: 相對路徑的基礎路徑

### Plugin Entry Schema

**必要欄位**：
```json
{
  "name": "plugin-identifier",
  "source": "string|object"
}
```

**選用欄位**：
- `description`, `version`, `author`, `homepage`
- `repository`, `license`, `keywords`
- `category`, `tags`, `strict` (boolean)
- `commands`, `agents`, `hooks`, `mcpServers`

## Plugin Source Types

### 1. 相對路徑
```json
{
  "name": "local-plugin",
  "source": "./plugins/my-plugin"
}
```

### 2. GitHub 儲存庫
```json
{
  "name": "github-plugin",
  "source": {
    "source": "github",
    "repo": "owner/plugin-repo"
  }
}
```

### 3. Git 儲存庫
```json
{
  "name": "git-plugin",
  "source": {
    "source": "url",
    "url": "https://gitlab.com/team/plugin.git"
  }
}
```

## 開發流程

### 新增 Plugin

1. 在 `plugins/` 目錄建立新的 plugin 資料夾
2. 建立 `SKILL.md` 和必要的 references/assets
3. 使用 skill-creator 驗證和打包
4. 更新 `.claude-plugin/marketplace.json`

### 驗證與測試

```bash
# 驗證 plugin 結構
python3 /path/to/skill-creator/scripts/package_skill.py ./plugins/plugin-name

# 本地測試
/plugin marketplace add ./path/to/claude-code-plugin
/plugin install plugin-name@vibe-plugins
```

### 發布流程

1. 確認所有變更已 commit
2. 更新 marketplace.json 的版本號
3. Push 到 GitHub
4. 使用者透過 GitHub 安裝最新版本

## 常用指令

### Marketplace 管理
```bash
/plugin marketplace add vivalalova/claude-code-plugin  # 安裝此 marketplace
/plugin marketplace list                               # 列出所有 marketplace
/plugin marketplace update vibe-plugins                # 更新
/plugin marketplace remove vibe-plugins                # 移除
```

### Plugin 操作
```bash
/plugin install agent-ide@vibe-plugins                 # 安裝 agent-ide
/plugin                                                # 瀏覽可用 plugins
```

## 團隊配置

團隊成員可在 `.claude/settings.json` 中預設安裝此 marketplace：

```json
{
  "extraKnownMarketplaces": {
    "vibe-plugins": {
      "source": {
        "source": "github",
        "repo": "vivalalova/claude-code-plugin"
      }
    }
  }
}
```

## 進階配置

當 `strict: false` 時，marketplace entries 可作為完整的 manifest：

```json
{
  "name": "enterprise-tools",
  "source": {"source": "github", "repo": "company/plugin"},
  "commands": ["./commands/core/", "./commands/enterprise/"],
  "agents": ["./agents/security-reviewer.md"],
  "hooks": {...},
  "mcpServers": {...},
  "strict": false
}
```

使用 `${CLAUDE_PLUGIN_ROOT}` 環境變數來引用安裝相對路徑。

## 技術參考

### marketplace.json Schema 說明

## 疑難排解檢查清單

- 確認 marketplace URL 可存取
- 確認 `.claude-plugin/marketplace.json` 存在
- 驗證 JSON 語法正確性
- 檢查儲存庫存取權限
- 確保 plugin source URLs 可達
- 確認 plugin 目錄包含必要檔案
