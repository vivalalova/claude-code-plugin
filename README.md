# Vibe Plugins for Claude Code

> Vibe 團隊的 Claude Code 插件市場，提供程式碼分析、重構和品質管理工具

## 📦 包含的 Plugins

### [agent-ide](./plugins/agent-ide)

程式碼重構與分析專家工具集，提供完整的程式碼智能功能：

- **🔍 快速診斷**：使用 snapshot 快速了解專案結構和品質
- **✏️ 安全重構**：符號重命名、檔案移動並自動更新所有引用
- **📊 品質分析**：ShitScore 評分、複雜度分析、死代碼偵測
- **🔗 依賴管理**：依賴關係分析、循環依賴檢測
- **🚀 CI/CD 整合**：質量閘門、GitHub Actions 支援
- **📝 完整文檔**：10 個實戰範例和完整 CLI 參考

支援 TypeScript 和 JavaScript 專案。

## 🚀 快速開始

### 安裝 Marketplace

```bash
# 從 GitHub 安裝
/plugin marketplace add vivalalova/claude-code-plugin
```

### 安裝 Plugin

```bash
# 安裝 agent-ide
/plugin install agent-ide@vibe-plugins

# 瀏覽所有可用 plugins
/plugin
```

### 使用範例

```bash
# 快速診斷專案
npx agent-ide snapshot --compression full

# 安全重命名符號
npx agent-ide rename --from oldName --to newName --preview
npx agent-ide rename --from oldName --to newName

# 檢查程式碼品質
npx agent-ide shit --detailed --top=20

# 分析依賴關係
npx agent-ide deps --detect-circular
```

## 📚 詳細文檔

- [agent-ide SKILL.md](./plugins/agent-ide/SKILL.md) - 完整功能說明和工作流程
- [CLI 指令參考](./plugins/agent-ide/references/cli-reference.md) - 所有指令的詳細參數
- [使用範例集](./plugins/agent-ide/references/examples.md) - 10 個實戰範例

## 🛠️ 開發指南

### 專案結構

```
.
├── .claude-plugin/
│   └── marketplace.json          # Marketplace 核心配置
├── plugins/
│   └── agent-ide/                # agent-ide plugin
│       ├── SKILL.md
│       ├── references/
│       └── agent-ide.zip
├── CLAUDE.md                     # Claude Code 工作指南
└── README.md                     # 本文件
```

### 新增 Plugin

1. 在 `plugins/` 目錄建立新的 plugin 資料夾
2. 建立 `SKILL.md` 和必要的 references/assets
3. 使用 skill-creator 驗證和打包：
   ```bash
   python3 /path/to/skill-creator/scripts/package_skill.py ./plugins/plugin-name
   ```
4. 更新 `.claude-plugin/marketplace.json`

### 本地測試

```bash
# 從本地路徑安裝 marketplace
/plugin marketplace add /path/to/claude-code-plugin

# 安裝 plugin
/plugin install plugin-name@vibe-plugins
```

## 🔧 團隊配置

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

## 📖 相關資源

- [Claude Code 文檔](https://docs.claude.com/en/docs/claude-code)
- [Plugin Marketplaces 指南](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
- [agent-ide GitHub](https://github.com/vivalalova/agent-ide)

## 📄 授權

MIT License

## 👤 作者

**Lova**

- GitHub: [@vivalalova](https://github.com/vivalalova)

---

💡 **提示**：查看 [CLAUDE.md](./CLAUDE.md) 獲取更多技術細節和 marketplace 配置說明。
