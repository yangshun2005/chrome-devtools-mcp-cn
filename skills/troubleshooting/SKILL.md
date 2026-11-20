---
name: troubleshooting
description: 使用 Chrome DevTools MCP 和文档排查连接与目标问题。当 list_pages、new_page 或 navigate_page 失败，或服务器初始化失败时触发此技能。
---

## 排查向导

你作为排查向导，帮助用户配置并修复 Chrome DevTools MCP 服务器设置。当此技能被触发时（例如 `list_pages`、`new_page` 或 `navigate_page` 失败，或服务器无法启动），按以下步骤进行诊断：

### 第 1 步：查找并读取配置

第一步是定位并读取 MCP 配置文件。在用户工作区中搜索以下文件：`.mcp.json`、`.vscode/mcp.json`、`gemini-extension.json` 或 `.gemini/settings.json`（若可访问，也包括用户级文件如 `~/.claude.json`）。

找到配置文件后，读取并解读，识别潜在问题，例如：

- 参数或标志错误。
- 缺少环境变量。
- 在不兼容的环境中使用了 `--autoConnect`。

只有在找不到任何上述文件时，才应请用户提供其配置文件内容。

### 第 2 步：分类常见连接错误

在阅读文档或建议修改配置之前，先检查错误信息是否匹配以下常见模式之一。

#### 错误：`Could not find DevToolsActivePort`

此错误高度特定于 `--autoConnect` 功能。它表示 MCP 服务器找不到由正在运行且可调试的 Chrome 实例创建的文件。这不是通用的连接失败。

首要目标是引导用户确保 Chrome 正在运行且配置正确。不要立即建议改用 `--browserUrl`。严格按以下顺序操作：

1. **请用户确认正确的 Chrome 版本**（例如，若错误信息提到 "Chrome Canary"，则确认该版本）正在运行。
2. **若用户确认正在运行，指示其启用远程调试。** 明确说明 URL 和操作："请在 Chrome 中打开新标签页，访问 `chrome://inspect/#remote-debugging`，确保勾选 'Enable remote debugging' 复选框。"
3. **用户确认以上两步后，下一步操作只能是调用 `list_pages` 工具。** 这是验证连接是否成功的最简单、最安全的方式。此时不要重试原始的复杂命令。
4. **若 `list_pages` 成功，问题已解决。** 若仍以相同错误失败，再进入更高级的步骤，如建议 `--browserUrl` 或检查沙箱问题。

#### 症状：服务器启动但创建了新的空配置文件

若服务器成功启动，但 `list_pages` 返回空列表或创建了新配置文件而非连接到现有 Chrome 实例，检查参数是否有拼写错误。

- **检查标志拼写错误：** 例如 `--autoBronnect` 而非 `--autoConnect`。
- **验证配置：** 确保参数与预期标志完全一致。

#### 症状：工具缺失 / 仅有少量工具可用

若服务器成功启动，但仅有少量工具（如 `list_pages`、`list_console_messages`、`get_console_message`、`list_network_requests`）可用，很可能是因为 MCP 客户端启用了**只读模式**。

`chrome-devtools-mcp` 中的所有工具都标注了 `readOnlyHint: true`（安全、非修改性工具）或 `readOnlyHint: false`（修改浏览器状态的工具，如 `emulate`、`click`、`navigate_page`）。要访问完整工具集，用户必须在 MCP 客户端中禁用只读模式（例如在 Gemini CLI 中退出 "Plan Mode"，或调整客户端的工具安全设置）。

#### 症状：扩展工具缺失或扩展加载失败

若与扩展相关的工具（如 `install_extension`）不可用，或加载的扩展无法正常工作：

1. **检查 `--categoryExtensions` 标志**：确保 MCP 服务器配置中传入了此标志以启用扩展类工具。

#### 其他常见错误

从失败的工具调用或 MCP 初始化日志中识别其他错误信息：

- `Target closed`
- "Tool not found"（检查是否使用了 `--slim`，它仅启用导航、脚本求值和截图工具）。
- 缺少 `pageId`：页面级工具需要 `pageId` 参数。调用 `list_pages` 查找活动页面 ID。
- `ProtocolError: Network.enable timed out` 或 `The socket connection was closed unexpectedly`
- `Error [ERR_MODULE_NOT_FOUND]: Cannot find module`
- 任何沙箱或主机验证错误。

### 第 3 步：阅读已知问题

阅读 https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/troubleshooting.md 的内容，将错误映射到已知问题。特别注意：

- 沙箱限制（macOS Seatbelt、Linux 容器）。
- WSL 要求。
- `--autoConnect` 握手、超时和要求（需要**正在运行**的 Chrome 144+）。

### 第 4 步：制定配置

根据具体错误和用户环境（操作系统、MCP 客户端），制定正确的 MCP 配置片段。检查是否需要：

- 传入 `--browser-url=http://127.0.0.1:9222` 而非 `--autoConnect`（例如在 Claude Desktop 等沙箱环境中）。
- 在 Chrome 中启用远程调试（`chrome://inspect/#remote-debugging`）并接受连接提示。**若使用 `--autoConnect`，请用户确认已启用此功能。**
- 添加 `--logFile <absolute_path_to_log_file>` 以捕获调试日志供分析。
- 若在 Windows 上使用 Codex，增大 `startup_timeout_ms`（例如至 20000）。

_若不确定用户配置，请用户提供其当前 MCP 服务器 JSON 配置。_

### 第 5 步：运行诊断命令

若问题仍不明确，运行诊断命令直接测试服务器：

- 运行 `npx chrome-devtools-mcp@latest --help` 验证安装和 Node.js 环境。
- 若需更多信息，运行 `NODE_DEBUG=* npx chrome-devtools-mcp@latest --logFile=/tmp/cdm-test.log` 捕获详细日志。分析输出中的错误。

### 第 6 步：在 GitHub 上检查已有 Issue

若 https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/troubleshooting.md 未涵盖该特定错误，检查环境中是否有 `gh`（GitHub CLI）工具。若有，在 GitHub 仓库中搜索类似 Issue：
`gh issue list --repo ChromeDevTools/chrome-devtools-mcp --search "<error snippet>" --state all`

或者，建议用户查看 https://github.com/ChromeDevTools/chrome-devtools-mcp/issues 和 https://github.com/ChromeDevTools/chrome-devtools-mcp/discussions 寻求帮助。