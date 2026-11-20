---
name: memory-leak-debugging
description: 诊断并解决 JavaScript/Node.js 应用中的内存泄漏。当用户报告内存占用过高、OOM 错误，或希望使用 Chrome DevTools MCP 内存工具捕获、比较、检查堆快照时使用。
---

# 内存泄漏调试

本技能提供专家指导和工作流，帮助使用 Chrome DevTools MCP 工具查找、诊断并修复 JavaScript 和 Node.js 应用中的内存泄漏。

## 前提条件

高级内存调试工具（`compare_heapsnapshots`、`get_heapsnapshot_details` 等）仅在服务器以 `--memoryDebugging` 标志启动时可用。先检查这些工具是否可用；若不可用，尝试读取 MCP 配置文件，确认是否启用了 `--memoryDebugging`。

## 核心原则

- **优先使用 MCP 内存工具：** 不要直接读取原始 `.heapsnapshot` 文件，它们极大，会消耗过多 token。使用 Chrome DevTools MCP 堆快照工具来汇总、比较和检查快照。
- **隔离泄漏：** 判断泄漏发生在浏览器（客户端）还是 Node.js（服务端）。
- **常见元凶：** 查找游离 DOM 节点、未处理的闭包、全局变量、未移除的事件监听器，以及无界增长的缓存。_注意：游离 DOM 节点有时是有意为之的缓存；置空之前务必先询问用户。_
- **关闭已加载的快照：** 堆快照可能很大。调查完成后，对每个已加载的快照调用 `close_heapsnapshot`，释放 MCP 服务器占用的内存。

## 工作流

### 1. 捕获快照

调查前端 Web 应用内存泄漏时，使用 `chrome-devtools-mcp` 工具与应用交互并拍摄快照。

- 使用页面级工具（如 `click`、`navigate_page`、`fill` 等，需指定 `pageId`）将页面操作到目标状态。
- 交互后将页面还原到原始状态，观察内存是否释放。
- 重复同一用户交互 10 次，放大泄漏。
- 使用 `take_heapsnapshot`（指定 `pageId`）在基线、目标（操作后）和最终（还原操作后）三个状态将 `.heapsnapshot` 文件保存到磁盘。

### 2. 比较快照

用 `take_heapsnapshot` 生成 `.heapsnapshot` 文件后，用 Chrome DevTools MCP 内存工具进行比较。

- 先对每个快照调用 `get_heapsnapshot_summary`，确认文件能加载，并比较高层级总量。
- 用 `compare_heapsnapshots` 比较基线和目标快照。先不带 `classIndex` 获取汇总差异，再仅对可疑增长指定 `classIndex` 请求详细的类差异。
- 先看 `compare_heapsnapshots` 的汇总输出，再深入具体节点 ID。

### 3. 检查保留者和支配者链

当某个类或对象类型异常增长时，先修改代码之前，用 MCP 工具检查保留链和支配者。

- 用 `get_heapsnapshot_class_nodes` 列出可疑类的实例。
- 用 `get_heapsnapshot_retainers`、`get_heapsnapshot_retaining_paths`、`get_heapsnapshot_dominators` 和 `get_heapsnapshot_edges` 理解代表性节点为何仍可达。
- 用 `get_heapsnapshot_object_details` 配合具体 `nodeId` 获取详细对象元数据（大小、类型、距离和 DOM 游离状态）。
- 当字符串增长在差异中占主导时，使用 `get_heapsnapshot_duplicate_strings`。
- 当保留路径指向应用代码后，阅读 [references/common-leaks.md](references/common-leaks.md) 了解常见内存泄漏示例及修复方法。

### 4. 高级分析与分类过滤器

使用内置 MCP 内存工具和过滤器直接定位特定泄漏类别，无需外部工具。

- 用 `get_heapsnapshot_details` 或 `get_heapsnapshot_class_nodes` 配合 `filterName` 定位常见泄漏原因：
  - `objectsRetainedByDetachedDomNodes`：识别内存中保留的游离 DOM 元素。
  - `objectsRetainedByEventHandlers`：识别被未移除的事件监听器保活的对象。
  - `objectsRetainedByContexts`：识别被困在闭包或执行上下文中的对象。
  - `objectsRetainedByConsole`：识别被控制台日志保留的对象。