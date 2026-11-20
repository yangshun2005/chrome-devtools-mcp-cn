---
name: inspect-in-devtools
description: 使用此技能连接 Chrome DevTools UI，进行交互式调试、UI/UX 审查、问答、向用户展示元素/网络请求/面板、检查用户当前在 DevTools 中的选择，以及处理或轮询 DevTools UI 评论线程。
---

# 在 DevTools 中调试、审查和问答

使用此技能可以：

1. **向用户展示目标**：打开 DevTools 并显示某个面板、DOM 元素或网络请求。
2. **检查用户的选择**：读取用户在 DevTools 中选中的元素或网络请求。
3. **处理 DevTools 评论**：获取评论线程，回答问题或应用修复，并解决线程。

## 前提条件

- **Chrome 版本**：Chrome M159 或更高版本。
- **必需标志**：MCP 服务器配置中的 `--devtools-comments`。缺少该标志时，`open_devtools`、`reveal_in_devtools`、`get_devtools_comments` 和 `resolve_devtools_comment` 不可用。
- **有头浏览器**：Chrome 不得以 `--headless` 模式运行。
- **页面定位**：所有工具都需要来自 `list_pages` 或 `new_page` 的 `pageId`。

## 工作流程

### 1. 打开 DevTools

使用 `pageId` 调用 `open_devtools`。在调用 `reveal_in_devtools`、`get_devtools_comments` 或 `resolve_devtools_comment` 之前先执行此操作。

### 2. 显示面板、元素或网络请求

使用 `pageId` 和以下可选参数调用 `reveal_in_devtools`：

- `panelName`：`"elements"`、`"network"`、`"sources"` 或 `"console"`。单独传入即可切换面板。
- `uid`：来自 `take_snapshot` 的元素 `uid`。在 Elements 面板中显示该元素。
- `reqid`：来自 `list_network_requests` 的请求 `reqid`。在 Network 面板中显示该请求。

`uid` 和 `reqid` 互斥。

### 3. 检查用户的选择

- **DOM 元素**：调用 `take_snapshot`。快照会标记 Elements 面板中选中的元素。将其 `uid` 传给 `get_css_styles`、`take_screenshot` 或 `evaluate_script`。
- **网络请求**：调用 `get_network_request` 并省略 `reqid`。它会返回 Network 面板中选中的请求。

### 4. 处理评论线程

根据用户请求选择模式。

#### 模式 A：一次性（评论已存在）

当用户表示评论已在 DevTools 中，或要求你不要轮询时，使用此模式。

1. 调用一次 `get_devtools_comments`。
2. 处理并解决每个返回的线程。
3. 在聊天中总结你的更改和回答，然后结束本轮。**不要**轮询。

#### 模式 B：监视（等待用户反馈）

当用户要求你在其调试、审查或添加评论期间等待时，使用此模式。

1. 立即调用 `get_devtools_comments`。
2. 当结果为 `"No open DevTools comments found."` 时，等待 10 秒后再次调用。若收到 `"DevTools comment threads updated"` 通知，则立即调用。
3. 连续 12 次空轮询（约 2 分钟）后停止，或当用户要求停止时停止。
4. 出现线程后，处理并解决它们。然后重置空轮询计数器并继续轮询。

轮询规则：

- **子代理工具可用时，使用后台子代理**，以保持主线程响应。子代理将线程返回给主代理，或在具备工作区和 MCP 工具时自行解决。
- **仅在子代理工具不可用时内联轮询**。不要提前结束本轮。
- **切勿在没有 10 秒延迟的情况下连续调用 `get_devtools_comments`**。

#### 处理并解决线程

对每个 `### Thread: <id>`：

1. **检查锚定目标**：
   - `Related element uid`：使用该 `uid` 调用 `get_css_styles`、`take_snapshot` 或 `take_screenshot`。
   - `Related network reqid`：使用该 `reqid` 调用 `get_network_request`。
   - 可选：调用 `reveal_in_devtools` 向用户展示目标。
2. **处理评论**：
   - 对于代码或样式请求，在工作区中应用修复。如有需要，重新加载并验证页面。
   - 对于问题或根因调查，收集诊断数据。不要进行不必要的代码编辑。
3. **解决**：使用 `pageId`、`threadId` 和 `replyText` 调用 `resolve_devtools_comment`：
   - 对于问题或根因，写出详细回答。
   - 对于简单修复，用 1–2 句话说明更改内容和文件。