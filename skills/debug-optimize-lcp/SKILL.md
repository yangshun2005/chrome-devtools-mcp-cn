---
name: debug-optimize-lcp
description: 指导使用 Chrome DevTools MCP 工具调试和优化最大内容绘制（LCP）。当用户询问 LCP 性能、页面加载缓慢、Core Web Vitals 优化，或想了解页面主内容为何迟迟不显示时，使用本技能。当用户提到"largest contentful paint"、"页面加载速度"、"CWV"，或想提升首屏大图或主内容的渲染速度时，也应使用本技能。
---

## 什么是 LCP，为何重要

最大内容绘制（LCP）衡量页面主内容多快变为可见。它指从导航开始到视口内最大图片或文本块渲染完成的时间。

- **良好**：2.5 秒或更短
- **需要改进**：2.5–4.0 秒
- **差**：大于 4.0 秒

LCP 是 Core Web Vital，直接影响用户体验和搜索排名。在 73% 的移动页面上，LCP 元素是图片。

## LCP 子部分拆解

每个页面的 LCP 都拆分为四个连续子部分，无间隙、无重叠。找出哪个子部分是瓶颈，是有效优化的关键。

| 子部分 | 理想 LCP 占比 | 衡量内容 |
| ----------------------------- | -------------- | ---------------------------------------------- |
| **首字节时间（TTFB）** | ~40% | 导航开始 → 收到 HTML 首字节 |
| **资源加载延迟** | <10% | TTFB → 浏览器开始加载 LCP 资源 |
| **资源加载时长** | ~40% | 下载 LCP 资源的时间 |
| **元素渲染延迟** | <10% | LCP 资源下载完成 → LCP 元素渲染完成 |

"延迟"类子部分应尽可能接近零。若任一延迟子部分相对总 LCP 占比较大，那就是首要优化点。

**常见陷阱**：只优化一个子部分（如压缩图片以缩短加载时长）而不检查其他子部分。如果渲染延迟才是真正的瓶颈，更小的图片也无济于事——省下的时间只会转移到渲染延迟上。

## 调试流程

按顺序执行以下步骤。每一步都基于前一步。

### 第 1 步：录制性能跟踪

导航到页面，然后录制带重新加载的跟踪，以捕获包含 LCP 的完整页面加载：

1. 用 `navigate_page` 和 `pageId` 导航到目标 URL。
2. 用 `performance_start_trace`，传入 `pageId`、`reload: true` 和 `autoStop: true`。

跟踪结果会包含 LCP 时序和可用的洞察集。记下输出中的洞察集 ID——下一步会用到。

### 第 2 步：分析 LCP 洞察

用 `performance_analyze_insight` 深入查看 LCP 相关洞察。在跟踪结果中查找以下洞察名称：

- **LCPBreakdown** — 显示四个 LCP 子部分及各自耗时。
- **DocumentLatency** — 影响 TTFB 的服务器响应时间问题。
- **RenderBlocking** — 阻塞 LCP 元素渲染的资源。
- **LCPDiscovery** — LCP 资源是否可被尽早发现。

调用 `performance_analyze_insight`，传入 `pageId`、洞察集 ID 和跟踪结果中的洞察名称。

### 第 3 步：识别 LCP 元素

用 `evaluate_script`（传入 `pageId`）和 [references/lcp-snippets.md](references/lcp-snippets.md) 中的 **"Identify LCP Element" 代码片段**，获取 LCP 元素的标签、资源 URL 和原始时序数据。

`url` 字段告诉你应在网络瀑布图中查找哪个资源。若 `url` 为空，则 LCP 元素是文本（无需加载资源）。

### 第 4 步：检查网络瀑布图

用 `list_network_requests` 查看 LCP 资源相对其他资源的加载时间：

- 调用 `list_network_requests`，传入 `pageId`，用 `resourceTypes: ["image", "font"]` 过滤（根据第 3 步调整）。
- 然后用 `get_network_request`，传入 `pageId` 和 LCP 资源的请求 ID，获取完整详情。

**关键检查：**

- **开始时间**：与 HTML 文档和首个资源对比。若 LCP 资源比首个资源晚很多才开始，说明存在资源加载延迟需消除。
- **时长**：资源加载时长过大，说明文件太大或服务器太慢。

### 第 5 步：检查 HTML 常见问题

用 `evaluate_script`（传入 `pageId`）和 [references/lcp-snippets.md](references/lcp-snippets.md) 中的 **"Audit Common Issues" 代码片段**，检查视口内是否有懒加载图片、是否缺少 fetchpriority、是否有阻塞渲染的脚本。

## 优化策略

找出瓶颈子部分后，按优先级应用以下修复。

### 1. 消除资源加载延迟（目标：<10%）

最常见的瓶颈。LCP 资源应立即开始加载。

- **根因**：LCP 图片通过 JS/CSS 加载、使用 `data-src`，或设置了 `loading="lazy"`。
- **修复**：使用标准 `<img>` 加 `src`。**绝不**对 LCP 图片懒加载。
- **修复**：若图片在 HTML 中无法被发现，添加 `<link rel="preload" fetchpriority="high">`。
- **修复**：为 LCP 的 `<img>` 标签添加 `fetchpriority="high"`。

### 2. 消除元素渲染延迟（目标：<10%）

元素应在加载完成后立即渲染。

- **根因**：大型样式表、`<head>` 中的同步脚本，或主线程阻塞。
- **修复**：内联关键 CSS，延迟非关键 CSS/JS。
- **修复**：拆分阻塞主线程的长任务。
- **修复**：使用服务端渲染（SSR），让元素存在于初始 HTML 中。

### 3. 缩短资源加载时长（目标：~40%）

让资源更小或传输更快。

- **修复**：使用现代格式（WebP、AVIF）和响应式图片（`srcset`）。
- **修复**：通过 CDN 提供资源。
- **修复**：设置 `Cache-Control` 响应头。
- **修复**：若 LCP 是受 Web 字体阻塞的文本，使用 `font-display: swap`。

### 4. 降低 TTFB（目标：~40%）

HTML 文档本身到达太慢。

- **修复**：减少重定向，优化服务器响应时间。
- **修复**：在边缘（CDN）缓存 HTML。
- **修复**：确保页面符合往返缓存（bfcache）条件。

## 验证修复与模拟

- **验证**：重新运行跟踪（`performance_start_trace`，传入 `pageId` 和 `reload: true`），对比新的子部分拆解。瓶颈应缩小。
- **模拟**：实验室测量与真实体验不同。用 `emulate` 在受限条件下测试：
  - `emulate`，传入 `pageId`、`networkConditions: "Fast 3G"` 和 `cpuThrottlingRate: 4`。
  - 这能暴露仅在较慢网络/设备上才可见的问题。