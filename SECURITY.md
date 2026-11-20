## 安全策略

Chrome DevTools MCP 项目非常重视安全。请使用 [Google 开源软件漏洞奖励计划](https://bughunters.google.com/about/rules/open-source/google-open-source-software-vulnerability-reward-program-rules) 报告任何安全问题。

### 范围

一般而言，使用此 MCP 服务器的 AI 代理或客户端应在发送任何输入（包括工具调用和参数）之前对其进行验证。服务器提供强大的浏览器自动化和检查能力，调用方代理有责任确保这些能力被安全且按预期使用。

本项目中的若干工具能够执行诸如将文件写入磁盘（例如通过浏览器下载或截图）、加载 source map 或动态加载 Chrome 扩展等操作。这些是有意为之的已记录功能，并非漏洞。

服务器以文本（类 Markdown）或结构化数据（`--experimentalStructuredContent`）形式向客户端返回网页内容。网页内容按原样返回以便调试，我们不认为基于网页内容导致的输出文本/Markdown 结构变化属于漏洞。如果结构对你的用例很重要，请使用结构化输出（`--experimentalStructuredContent`）。建议将此服务器与可信网页内容配合使用，或确保你的客户端对提示注入采取防范措施。

我们欢迎开发者就如何让此工具更易于构建更安全的用户体验提供反馈和建议，但会将其仅视为功能请求，而非 chrome-devtools-mcp 本身的漏洞。

### 可信主机环境

`chrome-devtools-mcp` 是本地开发者工具。它以启动它的用户权限运行，并信任其启动时所处的环境，这与在该环境中运行 `npm`、`npx`、`node` 或 `git` 时的行为相同。具体而言，我们将以下内容视为可信输入：

- 服务器启动时所在的当前工作目录及其内容。
- 环境变量，包括 `PATH`、`npm_config_*` 和代理设置。
- 用户在各作用域的 npm 配置（项目、用户和全局位置的 `.npmrc` 文件）及其解析到的 `npm` 可执行文件。我们有意通过 shell 调用 `npm`，以便更新检查遵循用户的 registry、代理和认证配置。
- Node.js 安装以及服务器启动或连接的 Chrome 二进制文件。

需要攻击者控制上述任何一项的报告，不视为 `chrome-devtools-mcp` 的漏洞。这包括将可执行文件或配置文件（例如 `npm.cmd` 或 `.npmrc`）放入服务器随后启动的工作区目录。不要在你不信任的工作区中启动服务器或任何其他开发者工具。MCP 客户端通常将打开的工作区作为 stdio 服务器的工作目录；如需检查不受信任的仓库，请在操作系统级沙箱或专用 VM 中运行服务器。可通过 `CHROME_DEVTOOLS_MCP_NO_UPDATE_CHECKS` 环境变量禁用启动时的更新检查。

### MCP roots

`chrome-devtools-mcp` 支持 [MCP roots](https://modelcontextprotocol.io/specification/2025-06-18/client/roots)。如果客户端指定了 roots，MCP 服务器在访问文件时会检查 roots。
注意，MCP 服务器始终保留对操作系统提供的 tmp 目录的访问权限。我们将 MCP roots 实现中的安全问题视为低严重性问题，因为它是可选配置。要实现完整的文件系统沙箱，我们建议使用操作系统沙箱机制。

### 网络防护

可选的 `--allowed-url-pattern` 和 `--blocked-url-pattern` 参数配置**浏览器**拒绝访问指定的 URL。注意，这不是完整的网络沙箱，仅在 `chrome-devtools-mcp` 附加到 Chrome DevTools 目标时对其生效。
要实现完整的网络沙箱，我们建议使用独立的操作系统/VM 沙箱机制。

### JavaScript 求值

可选的 `--no-javascript-evaluation` 参数禁用特定的服务器入口点，这些入口点用于求值调用方提供的 JavaScript：`evaluate_script` 工具（以及精简版 `evaluate` 工具）、`navigate_page` 的 `initScript` 参数，以及导航到 `javascript:`、`data:` 或 `vbscript:` URL。它不是 JavaScript 沙箱，也不构成浏览器范围的执行边界。页面自有的脚本，以及通过其他允许的浏览器交互（例如点击或输入）触发的 JavaScript，仍会运行。

`--experimentalDevtools` 参数启用 DevTools 目标的自动化，包括 DevTools 控制台，它可以在被检查页面中求值调用方输入的 JavaScript。`--no-javascript-evaluation` 不限制此 UI 路径。如果同时配置这两个选项，输入工具仍可通过 DevTools 在被检查页面中触发 JavaScript 求值。省略 `--experimentalDevtools` 可移除这一特定路径，但不提供完整的 JavaScript 隔离。

与其他工具一样，客户端有责任在发送工具调用和参数之前对其进行验证。