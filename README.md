

> **[中文化] chrome-devtools-mcp**
>
> 此项目是 [ChromeDevTools/chrome-devtools-mcp](https://github.com/ChromeDevTools/chrome-devtools-mcp) 的中文翻译版本。
> - 原项目 Stars: 58377
> - 主语言: TypeScript
> - 许可证: Apache-2.0
> - 翻译日期: 2026-11-20
> - 原始 README: [README_en.md](README_en.md)
>
> 说明：下方 README 正文为**原项目文档的中文翻译**，力求忠于原文；本页顶部的「中文快速上手」为**面向中文用户的补充导读**（非原文内容），目的是让你不用读完整篇 README 就能先把项目跑起来。
>
> 如有翻译不准确之处，欢迎提 Issue 或 PR。

---


## 🚀 中文快速上手（补充导读，非原文）

### 这是什么 / 解决什么问题

`chrome-devtools-mcp` 是一个 Model-Context-Protocol（MCP）服务器，同时附带一个 CLI，让 AI 编码助手（如 Antigravity、Claude、Cursor、Copilot）能够控制和检查一个正在运行的 Chrome 浏览器。它基于 Chrome DevTools 和 puppeteer，提供性能追踪分析、网络请求/截图/控制台消息调试，以及可靠的浏览器自动化能力。简单说，它把 Chrome DevTools 的能力接给 AI agent 使用。

### 快速上手（最短路径）

1. 确认本机已安装 Node.js LTS、npm 和 Chrome（当前稳定版或更新）。
2. 在你的 MCP 客户端配置中加入以下内容：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["-y", "chrome-devtools-mcp@latest"]
    }
  }
}
```

3. 针对具体编辑器/agent 的配置方式，原文指向 Client Configurations Guide（`docs/` 目录下，具体文件名原文未说明）。
4. 在 MCP 客户端里输入验证提示：

```
Check the performance of https://developers.chrome.com
```

5. 若客户端正常，应会自动打开浏览器并录制一次性能 trace。

> 注意：MCP 服务器只有在客户端调用需要浏览器的工具时才会自动启动浏览器；仅连接服务器本身不会启动浏览器。

如需不用 MCP、直接在终端控制浏览器，原文给出的 CLI 用法为：

```sh
npm i chrome-devtools-mcp@latest -g
chrome-devtools navigate_page 1 --url "https://developers.chrome.com"
chrome-devtools take_screenshot 1 --filePath screenshot.png
chrome-devtools stop
```

### 环境要求

- **操作系统**：原文未说明具体支持的操作系统列表。
- **硬件**：原文未说明。
- **依赖**：Node.js（LTS 版本）、npm、Chrome（当前稳定版或更新）。
- **浏览器限制**：官方仅支持 Google Chrome 和 Chrome for Testing；其他 Chromium 内核浏览器可能可用但不保证，可能出现异常行为。
- **版本要求**：Node.js 需为 LTS；Chrome 需当前稳定版或更新；项目对最新版 Extended Stable Chrome 提供修复与支持。
- **国内网络提示**：安装依赖走 npm，国内网络可能需要更换 npm 镜像源或配置加速下载（原文未提及镜像源，此为通用提示）。

### 常见坑与注意事项

- **隐私风险**：该工具会把浏览器实例内容暴露给 MCP 客户端，客户端可检查、调试、修改浏览器或 DevTools 中的任意数据，不要在其中共享敏感或个人信息。
- **默认开启数据上报**：Google 默认收集使用统计（工具调用成功率、延迟、环境信息等）。可用 `--no-usage-statistics` 关闭，或设置 `CHROME_DEVTOOLS_MCP_NO_USAGE_STATISTICS` / `CI` 环境变量关闭。
- **性能工具会外发数据**：性能工具可能把 trace URL 发送到 Google CrUX API 获取真实用户数据，可用 `--no-performance-crux` 关闭。
- **默认检查更新**：服务器会定期检查 npm 更新并提示，可用 `CHROME_DEVTOOLS_MCP_NO_UPDATE_CHECKS` 环境变量关闭。
- **浏览器不会自动启动**：仅连接 MCP 服务器不会启动浏览器，需客户端实际调用需要浏览器的工具才会启动。

---



---

# Chrome DevTools for agents

[![npm chrome-devtools-mcp package](https://img.shields.io/npm/v/chrome-devtools-mcp.svg)](https://npmjs.org/package/chrome-devtools-mcp)

Chrome DevTools for agents（`chrome-devtools-mcp`）让编码智能体（如 Antigravity、Claude、Cursor 或 Copilot）控制和检查运行中的 Chrome 浏览器。它提供 Model-Context-Protocol（MCP）服务器和 [CLI][cli] 两种形式。两者都能让你的 AI 编码助手获得 Chrome DevTools 的全部能力，用于可靠的自动化、深度调试和性能分析。

[工具参考][tool-reference] | [CLI][cli] | [技能][skills] | [更新日志][changelog] | [贡献][contributing] | [故障排查][troubleshooting] | [设计原则][design-principles]

## 主要特性

- **获取性能洞察**：使用 [Chrome DevTools](https://github.com/ChromeDevTools/devtools-frontend) 记录跟踪并提取可操作的性能洞察。
- **高级浏览器调试**：分析网络请求、截图并检查浏览器控制台消息（含 source map 堆栈跟踪）。
- **可靠的自动化**：使用 [puppeteer](https://github.com/puppeteer/puppeteer) 在 Chrome 中自动执行操作，并自动等待操作结果。

## 免责声明

`chrome-devtools-mcp` 会将浏览器实例的内容暴露给 MCP 客户端，使其能够检查、调试和修改浏览器或 DevTools 中的任何数据。请避免分享你不想让 MCP 客户端获取的敏感或个人信息。

`chrome-devtools-mcp` 官方仅支持 Google Chrome 和 [Chrome for Testing](https://developer.chrome.com/blog/chrome-for-testing/)。其他基于 Chromium 的浏览器可能可用，但不保证，且可能遇到意外行为。请自行斟酌使用。我们承诺为最新版本的 [Extended Stable Chrome](https://chromiumdash.appspot.com/schedule) 提供修复和支持。

性能工具可能会向 Google CrUX API 发送跟踪 URL，以获取真实用户体验数据。这有助于将现场数据与实验室数据并列呈现，从而提供全面的性能图景。这些数据由 [Chrome User Experience Report (CrUX)](https://developer.chrome.com/docs/crux) 收集。如需禁用，请使用 `--no-performance-crux` 标志运行。

## **使用统计**

Google 会收集使用统计（如工具调用成功率、延迟和环境信息），以改进 Chrome DevTools MCP 的可靠性和性能。

数据收集**默认启用**。你可以在启动服务器时传入 `--no-usage-statistics` 标志来退出：

```json
"args": ["-y", "chrome-devtools-mcp@latest", "--no-usage-statistics"]
```

Google 依据 [Google 隐私权政策](https://policies.google.com/privacy)处理这些数据。

Google 对 Chrome DevTools MCP 使用统计的收集独立于 Chrome 浏览器的使用统计。退出 Chrome 指标收集不会自动退出此工具，反之亦然。

如果设置了 `CHROME_DEVTOOLS_MCP_NO_USAGE_STATISTICS` 或 `CI` 环境变量，则收集会被禁用。

## 更新检查

默认情况下，服务器会定期检查 npm registry 是否有更新，并在有新版本可用时记录通知。你可以通过设置 `CHROME_DEVTOOLS_MCP_NO_UPDATE_CHECKS` 环境变量来禁用这些更新检查。

## 要求

- [Node.js](https://nodejs.org/) [LTS](https://github.com/nodejs/Release#release-schedule) 版本。
- [Chrome](https://www.google.com/chrome/) 当前稳定版或更新版本。
- [npm](https://www.npmjs.com/)

## 快速开始

将以下配置添加到你的 MCP 客户端：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["-y", "chrome-devtools-mcp@latest"]
    }
  }
}
```

> [!NOTE]
> 使用 `chrome-devtools-mcp@latest` 可确保你的 MCP 客户端始终使用最新版本的 Chrome DevTools MCP 服务器。

### MCP 客户端配置

针对你的编辑器或智能体（如 Antigravity、Claude Code、Cursor、VS Code）的具体设置说明，请参阅我们的[客户端配置指南][client-configurations-guide]。

### 你的第一个提示词

在 MCP 客户端中输入以下提示词，检查一切是否正常：

```
Check the performance of https://developers.chrome.com
```

你的 MCP 客户端应打开浏览器并记录性能跟踪。

> [!NOTE]
> 当 MCP 客户端使用需要运行中浏览器实例的工具时，MCP 服务器会自动启动浏览器。仅连接到 Chrome DevTools MCP 服务器不会自动启动浏览器。

## CLI

该包还包含 `chrome-devtools` CLI。你可以用它从终端控制浏览器，无需 MCP：

```sh
npm i chrome-devtools-mcp@latest -g
chrome-devtools navigate_page 1 --url "https://developers.chrome.com"
chrome-devtools take_screenshot 1 --filePath screenshot.png
chrome-devtools stop
```

所有命令和选项请参阅 [CLI 指南][cli]。

## 技能

该包包含[技能](./skills)，教你的智能体如何使用 Chrome DevTools：

| 技能                                                      | 用途                                            |
| --------------------------------------------------------- | ----------------------------------------------- |
| [`chrome-devtools`](./skills/chrome-devtools)             | 使用 MCP 工具调试和自动化页面。                 |
| [`chrome-devtools-cli`](./skills/chrome-devtools-cli)     | 运行 CLI 并用它编写 shell 脚本。                |
| [`a11y-debugging`](./skills/a11y-debugging)               | 审计并修复无障碍问题。                          |
| [`cookie-debugging`](./skills/cookie-debugging)           | 调试 Cookie、会话和身份验证。                   |
| [`debug-optimize-lcp`](./skills/debug-optimize-lcp)       | 调试并改进 Largest Contentful Paint。           |
| [`memory-leak-debugging`](./skills/memory-leak-debugging) | 查找并修复内存泄漏。                            |
| [`troubleshooting`](./skills/troubleshooting)             | 修复 MCP 与 Chrome 之间的连接问题。             |

### 以插件形式安装技能

插件会安装 MCP 服务器和所有技能，包括 CLI 技能：

- **Claude Code：** 运行 `/plugin marketplace add ChromeDevTools/chrome-devtools-mcp`，然后运行 `/plugin install chrome-devtools-mcp@chrome-devtools-plugins`。
- **Copilot / VS Code：** 运行 **Chat: Install Plugin From Source** 并输入 `ChromeDevTools/chrome-devtools-mcp`。
- **Gemini CLI：** 运行 `gemini extensions install --auto-update https://github.com/ChromeDevTools/chrome-devtools-mcp`。

更多信息请参阅[客户端配置指南][client-configurations-guide]。

### 手动安装技能

如果你的智能体不支持插件，请将技能文件夹复制到智能体的技能目录中。npm 包包含这些技能：

```sh
npm i chrome-devtools-mcp@latest -g
# Example for Claude Code. Use the skills directory of your agent.
mkdir -p ~/.claude/skills
cp -R "$(npm root -g)/chrome-devtools-mcp/skills/chrome-devtools-cli" ~/.claude/skills/
```

> [!IMPORTANT]
> `chrome-devtools-cli` 技能需要 CLI。使用该技能前请先全局安装该包。

### 保持技能更新

技能描述的是特定版本的工具。每次更新 MCP 服务器或 CLI 时，请同步更新技能。过时的技能可能会让你的智能体使用已变更的工具或参数。

- **插件：** 使用智能体的插件管理器更新命令。上面的 Gemini CLI 命令通过 `--auto-update` 启用自动更新。
- **手动安装：** 更新包，然后再次复制技能文件夹：

```sh
npm i chrome-devtools-mcp@latest -g
mkdir -p ~/.claude/skills
cp -R "$(npm root -g)/chrome-devtools-mcp/skills/chrome-devtools-cli" ~/.claude/skills/
```

## 工具

如果遇到任何问题，请查看我们的[故障排查指南][troubleshooting]。所有支持的 MCP 能力完整列表请参阅完整的[工具参考][tool-reference]。

## 配置

服务器参数的完整列表（如 `--headless`、`--isolated`、`--slim`）以及如何配置 WebSocket 连接，请参阅[配置指南][configuration-guide]。

仅用于基本浏览器任务时，`--slim` 标志会暴露一组精简的工具。请参阅[精简工具参考][slim-tool-reference]。

## 高级用法

关于处理并发会话、持久化用户数据目录、使用自定义或非标准浏览器路径（`--executablePath`）、连接到运行中的 Chrome 实例而非启动新实例，或在 Android 上调试等高级功能，请参阅我们的[高级用法指南][advanced-usage-guide]。

## 作为浏览器子智能体集成

如果你正在开发智能体工具，并希望在产品中提供集成的浏览器子智能体，我们建议基于 Chrome DevTools for agents 构建。

参考实现请参阅 [Gemini CLI 浏览器智能体文档](https://geminicli.com/docs/core/subagents/#browser-agent)。

[advanced-usage-guide]: ./docs/advanced-usage.md
[changelog]: ./CHANGELOG.md
[cli]: ./docs/cli.md
[client-configurations-guide]: ./docs/client-configurations.md
[configuration-guide]: ./docs/configuration.md
[contributing]: ./CONTRIBUTING.md
[design-principles]: ./docs/design-principles.md
[skills]: #skills
[slim-tool-reference]: ./docs/slim-tool-reference.md
[tool-reference]: ./docs/tool-reference.md
[troubleshooting]: ./docs/troubleshooting.md