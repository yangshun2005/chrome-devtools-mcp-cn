# Chrome DevTools CLI

`chrome-devtools-mcp` 包包含一个 CLI 接口，让你可以直接在终端中与浏览器交互。这在调试时特别有用，也适合让 agent 生成自动化浏览器操作的脚本。

## 快速开始

全局安装该包，使 `chrome-devtools` 命令可用：

```sh
npm i chrome-devtools-mcp@latest -g
chrome-devtools status # check if install worked.
```

CLI 默认允许不受限制的文件系统访问。使用 `--workspace` 将文件工具限制到特定目录。需要多个目录时重复该标志。
`--allow-unrestricted-paths` 仍可单独使用以保持兼容，但不能与 `--workspace` 组合。

```sh
chrome-devtools start --workspace=/path/to/project --workspace=/path/to/output
```

## Agent skill

[`chrome-devtools-cli`](../skills/chrome-devtools-cli) skill 教会你的 agent 如何使用 CLI。插件安装已包含此 skill。手动安装时，将 skill 文件夹复制到 agent 的 skills 目录：

```sh
# Example for Claude Code. Use the skills directory of your agent.
mkdir -p ~/.claude/skills
cp -R "$(npm root -g)/chrome-devtools-mcp/skills/chrome-devtools-cli" ~/.claude/skills/
```

所有 skill 及安装选项见 [Skills](../README.md#skills)。

> [!NOTE]
> 每次更新 CLI 后需重新复制 skill。见 [保持 skill 最新](../README.md#keep-skills-up-to-date)。

## 工作原理

CLI 作为后台 `chrome-devtools-mcp` 守护进程的客户端（Linux/Mac 使用 Unix socket，Windows 使用命名管道）。

- **自动启动**：首次调用工具（如 `list_pages`）时，若 MCP 服务器和浏览器尚未运行，CLI 会自动在后台启动它们。
- **持久化**：后续命令复用同一后台实例，保留浏览器状态（打开的页面、Cookie 等）。
- **手动控制**：可用 `start`、`stop`、`status` 显式管理后台进程。`start` 命令将后续参数转发给底层 MCP 服务器（如 `--headless`、`--userDataDir`），但并非所有参数都受支持。运行 `chrome-devtools start --help` 查看支持的参数。默认启用无头模式。除非提供 `--userDataDir`，否则默认启用隔离模式。

```sh
# Check if the daemon is running
chrome-devtools status

# Navigate page 1 to a URL
chrome-devtools navigate_page 1 --url "https://google.com"

# Take a screenshot of page 1 and save it to a file
chrome-devtools take_screenshot 1 --filePath screenshot.png

# Stop the background daemon when finished
chrome-devtools stop
```

## 命令用法

CLI 支持 MCP 服务器中的所有工具，即使某些工具在 MCP 服务器中需要标志才能激活（见[工具参考](./tool-reference.md)）。

```sh
chrome-devtools <tool> [arguments] [flags]
```

- **必需参数**：作为位置参数传入。页面级工具要求 `<pageId>` 作为第一个位置参数。
- **可选参数**：作为标志传入（如 `--filePath`、`--fullPage`）。

### 示例

**新建页面与导航：**

```sh
chrome-devtools new_page "https://example.com"
chrome-devtools navigate_page 1 --url "https://web.dev"
```

**交互：**

```sh
# Click an element by its UID from a snapshot on page 1
chrome-devtools click 1 "element-uid-123"

# Fill a form field on page 1
chrome-devtools fill 1 "input-uid-456" "search query"
```

**脚本求值：**

- 启用 `--categoryExtensions` 和 `--pageIdRouting` 时：
  - 用 `--pageId <number>` 指定页面：`chrome-devtools evaluate_script "() => document.title" --pageId 1`
  - 用 `--serviceWorkerId <string>` 指定扩展 service worker：`chrome-devtools evaluate_script "() => self.registration.scope" --serviceWorkerId sw-1`

```sh
# Evaluate a JavaScript expression on page 1
chrome-devtools evaluate_script "() => document.title" --pageId 1

# Evaluate inside an extension service worker
chrome-devtools evaluate_script "() => self.registration.scope" --serviceWorkerId sw-1
```

**分析：**

```sh
# Run a Lighthouse audit on page 1 (defaults to navigation mode)
chrome-devtools lighthouse_audit 1 --mode snapshot
```

## 输出格式

默认情况下，CLI 输出工具结果的人类可读摘要。用于程序化处理时，可请求原始 JSON：

```sh
chrome-devtools list_pages --output-format=json
```

## 故障排查

若 CLI 挂起或无法连接，尝试停止后台进程：

```sh
chrome-devtools stop
```

需要更详细的日志时，设置 `NODE_DEBUG` 环境变量。后台进程不向终端写入，因此用 `--logFile` 重启以将日志捕获到文件：

```sh
NODE_DEBUG=* chrome-devtools start --logFile=/tmp/chrome-devtools.log
chrome-devtools list_pages
```

## CLI 生成

实现在 `scripts/generate-cli.ts` 中。部分命令不参与 CLI 生成，如 `wait_for` 和 `fill_form`。

`chrome-devtools-mcp` 的参数也在 `src/bin/chrome-devtools.ts` 中过滤，因为并非所有参数在 CLI 接口中都有意义。