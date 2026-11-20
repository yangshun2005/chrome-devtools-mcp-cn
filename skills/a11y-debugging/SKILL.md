---
name: a11y-debugging
description: 使用 Chrome DevTools MCP 基于 web.dev 指南进行无障碍（a11y）调试与审计。适用于测试语义化 HTML、ARIA 标签、焦点状态、键盘导航、点击目标和颜色对比度。
---

## 核心概念

**页面定位**：页面级工具（`take_snapshot`、`list_console_messages`、`evaluate_script`、`press_key`、`take_screenshot`、`lighthouse_audit` 等）需要 `pageId` 参数。可通过 `list_pages` 或 `new_page` 获取可用页面 ID。

**无障碍树与 DOM**：视觉上隐藏元素（如 `CSS opacity: 0`）对屏幕阅读器的行为与 `display: none` 或 `aria-hidden="true"` 不同。`take_snapshot` 工具返回页面的无障碍树，它代表辅助技术"看到"的内容，是语义结构最可靠的事实来源。

**阅读 web.dev 文档**：如需查阅特定无障碍指南（如 `https://web.dev/articles/accessible-tap-targets`），可在 URL 后追加 `.md.txt`（如 `https://web.dev/articles/accessible-tap-targets.md.txt`）获取干净的原始 Markdown 版本。这样阅读起来轻松得多！

## 工作流模式

### 1. 自动化审计（Lighthouse）

先运行 Lighthouse 无障碍审计，获取全面的基线。该工具给出总体评分，并列出具体失败元素及修复建议。

1.  运行审计：
    - 将 `mode` 设为 `"navigation"` 以刷新页面并捕获加载问题。
    - 省略 `outputDirPath` 可将报告保存到默认临时目录（`report.json` 的确切路径会在 `Reports` 下返回），或将 `outputDirPath` 设为工作区内的目录。
2.  **分析摘要**：
    - 默认文本输出中，类别评分采用 0-100 分制（100 为满分），随后是 `Passed: N` 和 `Failed: N` 审计计数。
    - 若服务器以 `--experimentalStructuredContent` 启动，则改为检查结构化内容中的 `summary.scores[].score`（0-1 分制）和 `summary.audits.failed`。
3.  **查看报告（关键）**：
    - **解析**：不要逐行读取整个文件。使用 `jq` 或 Node.js 单行命令等 CLI 工具，从 `lighthouse_audit` 返回的报告路径中筛选失败项：
      ```bash
      # Extract failing audits with their details (replace with report.json path from tool response)
      node -e "const r=require('<path/to/report.json>'); Object.values(r.audits).filter(a=>a.score!==null && a.score<1).forEach(a=>console.log(JSON.stringify({id:a.id, title:a.title, items:a.details?.items})))"
      ```
    - 这样可高效提取失败元素的 `selector` 和 `snippet`，无需将完整报告载入上下文。

### 2. 浏览器问题与审计

Chrome 会自动检查常见的无障碍问题。使用 `list_console_messages` 检查这些原生审计：

- `types`：`["issue"]`
- `includePreservedMessages`：`true`（以捕获页面加载期间发生的问题）

这通常能揭示缺失标签、无效 ARIA 属性及其他关键错误，无需手动排查。

### 3. 语义与结构

无障碍树暴露标题层级和语义地标。

1.  导航到页面。
2.  使用 `take_snapshot` 捕获无障碍树。
3.  **检查标题层级**：确保标题层级（`h1`、`h2`、`h3` 等）逻辑合理且不跳级。快照会包含标题角色。
4.  **内容重排**：确认 DOM 顺序（决定无障碍树）与视觉阅读顺序一致。使用 `take_screenshot` 检查视觉布局并与快照结构对比，以发现打乱逻辑流的 CSS 浮动或绝对定位。

### 4. 标签、表单与文本替代

1.  在 `take_snapshot` 输出中定位按钮、输入框和图片。
2.  确保交互元素具有可访问名称（如仅含图标的按钮不应只显示 `""`）。
3.  **孤立输入框**：确认所有表单输入框都有关联标签。使用 `evaluate_script` 配合 [references/a11y-snippets.md](references/a11y-snippets.md) 中的 **"Find Orphaned Form Inputs" 代码片段**。
4.  检查图片的 `alt` 文本。

### 5. 焦点与键盘导航

在没有视觉反馈的情况下测试"键盘陷阱"和正确的焦点管理，依赖于跟踪焦点元素。

1.  使用 `press_key` 工具配合 `"Tab"` 或 `"Shift+Tab"` 移动焦点。
2.  使用 `take_snapshot` 捕获更新后的无障碍树。
3.  在快照中定位标记为焦点的元素，确认焦点已移到预期的交互元素。
4.  若打开模态框，焦点必须移入模态框并在关闭前"困"在其中。

### 6. 点击目标与视觉

根据 web.dev，点击目标应至少为 48x48 像素且间距充足。由于无障碍树不显示尺寸，使用 `evaluate_script` 配合 [references/a11y-snippets.md](references/a11y-snippets.md) 中的 **"Measure Tap Target Size" 代码片段**。

_将快照中元素的 `uid` 作为参数传给 `evaluate_script`。_

### 7. 颜色对比度

要验证颜色对比度，使用 `evaluate_script` 配合 [references/a11y-snippets.md](references/a11y-snippets.md) 中的 **"Check Color Contrast" 代码片段**。

### 8. 全局页面检查

使用 [references/a11y-snippets.md](references/a11y-snippets.md) 中的 **"Global Page Checks" 代码片段**，验证组件测试中常被遗漏的文档级无障碍设置。

## 故障排查

若标准 a11y 查询失败或 `evaluate_script` 代码片段返回意外结果：

- **视觉检查**：若自动化脚本无法确定对比度（如渐变图片或复杂背景上的文字），使用 `take_screenshot` 捕获该元素。虽然模型无法从图片中测量精确的对比度，但可以视觉评估可读性并识别明显问题。