---
name: chrome-devtools
description: 通过 MCP 使用 Chrome DevTools 进行高效调试、故障排查和浏览器自动化。适用于调试网页、自动化浏览器交互、分析性能或检查网络请求。本技能不适用于 `--slim` 模式（MCP 配置）。
---

## 核心概念

**浏览器生命周期**：首次调用工具时，浏览器会使用持久化的 Chrome 配置文件自动启动。可通过 MCP 服务器配置中的 CLI 参数进行配置：`npx chrome-devtools-mcp@latest --help`。
提供以下标志可启用额外工具：

- 扩展工具：使用 `--categoryExtensions` 标志。
- 内存工具：使用 `--memoryDebugging` 标志。

**页面定位**：页面级工具需要 `pageId` 参数来定位特定页面。使用 `list_pages` 查看可用页面及其 ID（例如 `pageId: 1`），或使用 `new_page` 创建页面时返回的 ID。
注意：对于 `evaluate_script`，定位页面时 `pageId` 为必填。但启用 `--categoryExtensions` 后，`pageId` 变为可选，此时可传入 `serviceWorkerId` 在扩展后台 Service Worker 中执行求值。
**元素交互**：使用 `take_snapshot` 获取页面结构及元素的 `uid`。每个元素都有唯一的 `uid` 用于交互。若找不到元素，请重新获取快照——元素可能已被移除或页面已变化。

## 工作流模式

### 与页面交互之前

1. 导航：`navigate_page` 或 `new_page`
2. 等待：若已知目标内容，使用 `wait_for` 确保内容已加载。
3. 快照：使用带 `pageId` 的 `take_snapshot` 了解页面结构
4. 交互：使用快照中的元素 `uid` 执行 `click`、`fill` 等操作，并传入对应的 `pageId`。

### 高效获取数据

- 大体积输出（截图、快照、追踪）使用 `filePath` 参数
- 使用分页（`pageIdx`、`pageSize`）和过滤（`types`）减少数据量
- 除非需要更新后的页面状态，否则在输入操作上设置 `includeSnapshot: false`

### 工具选择

- **网络模拟**：`mock_network` 和 `remove_network_mock` 需要在服务器启动时启用 `--categoryNetworkMocks`。
- **自动化/交互**：`take_snapshot`（基于文本，更快，更适合自动化）
- **视觉检查**：`take_screenshot`（用户需要查看视觉状态时）
- **CSS 与样式检查**：使用 `get_css_styles` 检查计算样式、匹配规则、层叠和 CSS 变量（传入 `computedProperties` 可获取未匹配的默认值）。以输出为准。
- **补充细节**：`get_css_styles` 或 `take_snapshot` 无法获取的运行时 DOM/JS 数据，使用 `evaluate_script`。
- **运行时值**：使用 `set_logpoints` 在脚本或源文件行（支持 source map）记录表达式，无需修改代码；通过 `list_console_messages` 读取输出。

### 并行执行

可以并行发送多个工具调用，但需保持正确顺序：导航 → 等待 → 快照 → 交互。

### 测试扩展

> **继续之前**：扩展工具（`install_extension`、`list_extensions` 等）仅在 MCP 服务器以 `--categoryExtensions` 标志启动时可用。若工具列表中无这些工具，请停止操作并要求用户更新 MCP 服务器配置：
>
> ```json
> {
>   "mcpServers": {
>     "chrome-devtools": {
>       "command": "npx",
>       "args": ["chrome-devtools-mcp@latest", "--categoryExtensions"]
>     }
>   }
> }
> ```
>
> 更新后，用户必须重启 MCP 服务器（或其 AI 客户端）才能使更改生效。

1. **安装**：使用 `install_extension` 并传入未打包扩展的路径。
2. **识别**：从响应中或调用 `list_extensions` 获取扩展 ID。
3. **触发操作**：如适用，使用 `trigger_extension_action` 打开弹窗或侧边栏。
4. **验证 Service Worker**：使用带 `serviceWorkerId` 的 `evaluate_script`（省略 `pageId` 和 `args`）检查扩展状态或触发后台操作。在页面中求值时，传入 `pageId`（省略 `serviceWorkerId`）。
5. **验证页面行为**：导航到扩展运行的页面，使用 `take_snapshot` 检查内容脚本是否正确注入元素或修改页面。

## 故障排查

若 `chrome-devtools-mcp` 功能不足，引导用户使用 Chrome DevTools UI：

- https://developer.chrome.com/docs/devtools
- https://developer.chrome.com/docs/devtools/ai-assistance

若启动 `chrome-devtools-mcp` 或 Chrome 时出错，请参阅 https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/troubleshooting.md。