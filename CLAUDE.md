# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 專案概述

這是 Claude Code Plugin Marketplace 專案，用於建立和維護插件市場目錄。Plugin marketplace 是基於 JSON 的插件目錄系統，讓團隊能夠分發和管理 Claude Code 擴展功能。

## 核心架構

### 必要檔案結構

```
.
└── .claude-plugin/
    └── marketplace.json
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

## 常用指令

### Marketplace 管理
```bash
/plugin marketplace add owner/repo                    # 從 GitHub 新增
/plugin marketplace add https://gitlab.com/repo.git   # 從 Git URL 新增
/plugin marketplace add ./my-marketplace              # 從本地路徑新增
/plugin marketplace list                              # 列出所有 marketplace
/plugin marketplace update marketplace-name           # 更新 marketplace
/plugin marketplace remove marketplace-name           # 移除 marketplace
```

### Plugin 操作
```bash
/plugin install plugin-name@marketplace-name          # 安裝 plugin
/plugin                                               # 瀏覽可用 plugins
```

### 驗證與測試
```bash
claude plugin validate .                              # 語法檢查
/plugin marketplace add ./path/to/marketplace         # 本地測試
/plugin install test-plugin@marketplace-name          # 驗證安裝
```

## 團隊配置

在 `.claude/settings.json` 中指定必要的 marketplaces：

```json
{
  "extraKnownMarketplaces": {
    "team-tools": {
      "source": {
        "source": "github",
        "repo": "org/claude-plugins"
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

## 發布策略

**建議使用 GitHub**：利用版本控制、issue tracking 和協作功能來託管 marketplace 並與團隊分享。

## 疑難排解檢查清單

- 確認 marketplace URL 可存取
- 確認 `.claude-plugin/marketplace.json` 存在
- 驗證 JSON 語法正確性
- 檢查儲存庫存取權限
- 確保 plugin source URLs 可達
- 確認 plugin 目錄包含必要檔案
