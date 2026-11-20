# MCP 客户端配置

<!--
添加新的客户端配置时，请确保按字母顺序添加。
-->

<details>
  <summary>Amp</summary>

按照 https://ampcode.com/manual#mcp 操作，并使用[标准配置][standard-config]。也可以通过 CLI 安装 Chrome DevTools MCP 服务器：

```bash
amp mcp add chrome-devtools -- npx chrome-devtools-mcp@latest
```

</details>

<details>
  <summary>Antigravity</summary>

要使用 Chrome DevTools MCP 服务器，请按照 <a href="https://antigravity.google/docs/mcp">Antigravity 文档</a>中的说明安装自定义 MCP 服务器。将以下配置添加到 MCP 服务器配置中：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": [
        "-y",
        "chrome-devtools-mcp@latest",
        "--browser-url=http://127.0.0.1:9222"
      ]
    }
  }
}
```

这会让 Chrome DevTools MCP 服务器自动连接到 Antigravity 正在使用的浏览器。如果你使用的不是 9222 端口，请相应调整。

这种方式下 Chrome DevTools MCP 不会自动启动浏览器实例，因为 Chrome DevTools MCP 服务器连接的是 Antigravity 的内置浏览器。如果浏览器尚未运行，需先点击右上角的 Chrome 图标启动它。

如果你在 VS Code 中使用 Antigravity 扩展（而非独立的 Antigravity IDE），请在 Antigravity 的 MCP 配置（`mcp_config.json`）中配置服务器，而不是 VS Code 的 Copilot 设置。如果没有在 9222 端口上运行带远程调试的浏览器，请省略 `--browser-url`，让 Chrome DevTools MCP 自动启动 Chrome。

</details>

<details>
  <summary>Bob</summary>

按照 <a href="https://bob.ibm.com/docs/ide/configuration/mcp/mcp-in-bob">IBM Bob MCP 指南</a>操作，将 Chrome DevTools MCP 服务器添加到 Bob MCP 配置中。使用全局配置（`~/.bob/mcp.json`）可应用于所有工作区，或使用项目配置（`.bob/mcp.json`）限定到单个项目：

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

可以从 **Bob 面板 → Settings → MCP → Edit Global MCP**（或 **Edit Project MCP**）编辑这些文件。Bob 会在保存时热重载。服务器出现在 MCP 标签页后，切换到 **🌎 Browser Dev** 模式即可在 Bob 中直接获得引导式浏览器调试。

</details>

<details>
  <summary>Claude Code</summary>

**通过 CLI 安装（仅 MCP）**

使用 Claude Code CLI 添加 Chrome DevTools MCP 服务器（<a href="https://code.claude.com/docs/en/mcp">指南</a>）：

```bash
claude mcp add chrome-devtools --scope user npx chrome-devtools-mcp@latest
```

**作为插件安装（MCP + Skills）**

> [!NOTE]
> 如果你之前已为 Claude Code 安装过 Chrome DevTools MCP，请先从安装和配置文件中移除它。

要安装带 skills 的 Chrome DevTools MCP，请在 Claude Code 中添加市场注册表：

```sh
/plugin marketplace add ChromeDevTools/chrome-devtools-mcp
```

然后安装插件：

```sh
/plugin install chrome-devtools-mcp@chrome-devtools-plugins
```

重启 Claude Code 以加载 MCP 服务器和 skills（用 `/skills` 检查）。

> [!TIP]
> 如果插件安装失败并报 `Failed to clone repository` 错误（例如企业防火墙后的 HTTPS 连接问题），请参阅[故障排除指南](./troubleshooting.md#claude-code-plugin-installation-fails-with-failed-to-clone-repository)了解变通方法，或改用上面的 CLI 安装方式。

</details>

<details>
  <summary>Cline</summary>

按照 https://docs.cline.bot/mcp/configuring-mcp-servers 操作，并使用[标准配置][standard-config]。

</details>

<details>
  <summary>Codex</summary>

按照 <a href="https://developers.openai.com/codex/mcp/#configure-with-the-cli">配置 MCP 指南</a>操作，使用[标准配置][standard-config]。也可以通过 Codex CLI 安装 Chrome DevTools MCP 服务器：

```bash
codex mcp add chrome-devtools -- npx chrome-devtools-mcp@latest
```

**在 Windows 11 上**

更新 `.codex/config.toml`，添加以下 `env` 和 `startup_timeout_ms` 参数，以配置 Chrome 安装位置并增加启动超时：

```
[mcp_servers.chrome-devtools]
command = "cmd"
args = [
    "/c",
    "npx",
    "-y",
    "chrome-devtools-mcp@latest",
]
env = { SystemRoot="C:\\Windows", PROGRAMFILES="C:\\Program Files" }
startup_timeout_ms = 20_000
```

</details>

<details>
  <summary>Command Code</summary>

使用 Command Code CLI 添加 Chrome DevTools MCP 服务器（<a href="https://commandcode.ai/docs/mcp">MCP 指南</a>）：

```bash
cmd mcp add chrome-devtools --scope user npx chrome-devtools-mcp@latest
```

</details>

<details>
  <summary>Copilot CLI</summary>

启动 Copilot CLI：

```
copilot
```

运行以下命令启动添加新 MCP 服务器的对话框：

```
/mcp add
```

配置以下字段并按 `CTRL+S` 保存配置：

- **Server name:** `chrome-devtools`
- **Server Type:** `[1] Local`
- **Command:** `npx -y chrome-devtools-mcp@latest`

</details>

<details>
  <summary>Copilot / VS Code</summary>

**作为插件安装（推荐）**

最快捷的上手方式是将 `chrome-devtools-mcp` 安装为 agent 插件。
它打包了 **MCP 服务器**和所有 **skills**，让你的 agent 同时获得工具
以及有效使用这些工具所需的专家指导。

1.  打开**命令面板**（macOS 上为 `Cmd+Shift+P`，Windows/Linux 上为 `Ctrl+Shift+P`）。
2.  搜索并运行 **Chat: Install Plugin From Source** 命令。
3.  粘贴我们的仓库名：`ChromeDevTools/chrome-devtools-mcp`。

就这样！你的 agent 现在已具备 Chrome DevTools 能力。

---

**作为 MCP 服务器安装（仅 MCP）**

**点击按钮安装：**

[<img src="https://img.shields.io/badge/VS_Code-VS_Code?style=flat-square&label=Install%20Server&color=0098FF" alt="Install in VS Code">](https://vscode.dev/redirect/mcp/install?name=io.github.ChromeDevTools%2Fchrome-devtools-mcp&config=%7B%22command%22%3A%22npx%22%2C%22args%22%3A%5B%22-y%22%2C%22chrome-devtools-mcp%22%5D%2C%22env%22%3A%7B%7D%7D)

[<img src="https://img.shields.io/badge/VS_Code_Insiders-VS_Code_Insiders?style=flat-square&label=Install%20Server&color=24bfa5" alt="Install in VS Code Insiders">](https://insiders.vscode.dev/redirect?url=vscode-insiders%3Amcp%2Finstall%3F%257B%2522name%2522%253A%2522io.github.ChromeDevTools%252Fchrome-devtools-mcp%2522%252C%2522config%2522%253A%257B%2522command%2522%253A%2522npx%2522%252C%2522args%2522%253A%255B%2522-y%2522%252C%2522chrome-devtools-mcp%2522%255D%252C%2522env%2522%253A%257B%257D%257D%257D)

**或手动安装：**

按照 VS Code [MCP 配置指南](https://code.visualstudio.com/docs/copilot/chat/mcp-servers#_add-an-mcp-server)操作，使用[标准配置][standard-config]，或使用 CLI：

macOS 和 Linux：

```bash
code --add-mcp '{"name":"io.github.ChromeDevTools/chrome-devtools-mcp","command":"npx","args":["-y","chrome-devtools-mcp"],"env":{}}'
```

Windows（PowerShell）：

```powershell
code --add-mcp '{"""name""":"""io.github.ChromeDevTools/chrome-devtools-mcp""","""command""":"""npx""","""args""":["""-y""","""chrome-devtools-mcp"""]}'
```

</details>

<details>
  <summary>Cursor</summary>

**点击按钮安装：**

[<img src="https://cursor.com/deeplink/mcp-install-dark.svg" alt="Install in Cursor">](https://cursor.com/en/install-mcp?name=chrome-devtools&config=eyJjb21tYW5kIjoibnB4IC15IGNocm9tZS1kZXZ0b29scy1tY3BAbGF0ZXN0In0%3D)

**或手动安装：**

前往 `Cursor Settings` -> `MCP` -> `New MCP Server`。使用[标准配置][standard-config]。

</details>

<details>
  <summary>Devin CLI</summary>

**通过 CLI 安装（仅 MCP）**

使用 Devin CLI 添加 Chrome DevTools MCP 服务器（<a href="https://docs.devin.ai/cli/extensibility/mcp/configuration">指南</a>）：

```bash
devin mcp add chrome-devtools -- npx chrome-devtools-mcp@latest
```

</details>

<details>
  <summary>Factory CLI</summary>
使用 Factory CLI 添加 Chrome DevTools MCP 服务器（<a href="https://docs.factory.ai/cli/configuration/mcp">指南</a>）：

```bash
droid mcp add chrome-devtools "npx -y chrome-devtools-mcp@latest"
```

</details>

<details>
  <summary>FLUJO</summary>

要将 [FLUJO](https://flujo.com.co/) 连接到现有 Chrome 实例，请先
[启用远程调试启动 Chrome](./advanced-usage.md#manual-connection-using-port-forwarding)。
在下面的参数中使用该设置中的调试端口。

1. 安装 Node.js 后，在本地目录运行 `npm install chrome-devtools-mcp@latest`。
2. 在 FLUJO 中打开 **Connected Apps > Connect App > I'm an expert > Configure & Test**。
3. 将 **Server name** 设为 `chrome-devtools`，**MCP server root path** 设为安装包所在目录。
4. 选择 **Standard IO**，将 **Run command** 设为 `npx`。用 **Add argument** 分别输入 `-y`、`chrome-devtools-mcp@latest` 和 `--browser-url=http://127.0.0.1:9222`，必要时调整端口。
5. 点击 **3) Test run**。MCP 握手成功后，点击 **Add server**。
6. 打开已保存服务器的 **Tools** 标签页。用 **Test tool** 以要调试的 URL 调用 `new_page`，再用返回的页面 ID 调用 `take_snapshot` 验证浏览器访问。

</details>

<details>
  <summary>Gemini CLI</summary>
使用 Gemini CLI 安装 Chrome DevTools MCP 服务器。

**项目范围：**

```bash
# Either MCP only:
gemini mcp add chrome-devtools npx chrome-devtools-mcp@latest
# Or as a Gemini extension (MCP+Skills):
gemini extensions install --auto-update https://github.com/ChromeDevTools/chrome-devtools-mcp
```

**全局：**

```bash
gemini mcp add -s user chrome-devtools npx chrome-devtools-mcp@latest
```

或者，按照 <a href="https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/mcp-server.md#how-to-set-up-your-mcp-server">MCP 指南</a>操作，使用[标准配置][standard-config]。

</details>

<details>
  <summary>Gemini Code Assist</summary>

按照 <a href="https://cloud.google.com/gemini/docs/codeassist/use-agentic-chat-pair-programmer#configure-mcp-servers">配置 MCP 指南</a>操作，
使用[标准配置][standard-config]。

</details>

<details>
  <summary>Grok Build CLI</summary>

```bash
grok mcp add chrome-devtools npx chrome-devtools-mcp@latest
```

更多选项请参阅<a href="https://docs.x.ai/build/features/skills-plugins-marketplaces">文档</a>
</details>

<details>
  <summary>JetBrains AI Assistant & Junie</summary>

前往 `Settings | Tools | AI Assistant | Model Context Protocol (MCP)` -> `Add`。使用[标准配置][standard-config]。
同样地，可在 `Settings | Tools | Junie | MCP Settings` -> `Add` 中为 JetBrains Junie 配置 chrome-devtools-mcp。使用[标准配置][standard-config]。

</details>

<details>
  <summary>Kiro</summary>

在 **Kiro Settings** 中，前往 `Configure MCP` > `Open Workspace or User MCP Config` > 使用[标准配置][standard-config]。

或者，从 IDE **活动栏** > `Kiro` > `MCP Servers` > `点击 Open MCP Config`。使用[标准配置][standard-config]。

</details>

<details>
  <summary>Katalon Studio</summary>

Chrome DevTools MCP 服务器可通过 MCP 代理与 <a href="https://docs.katalon.com/katalon-studio/studioassist/mcp-servers/setting-up-chrome-devtools-mcp-server-for-studioassist">Katalon StudioAssist</a> 配合使用。

**第 1 步：** 按照 <a href="https://docs.katalon.com/katalon-studio/studioassist/mcp-servers/setting-up-mcp-proxy-for-stdio-mcp-servers">MCP 代理设置指南</a>安装 MCP 代理。

**第 2 步：** 通过代理启动 Chrome DevTools MCP 服务器：

```bash
mcp-proxy --transport streamablehttp --port 8080 -- npx -y chrome-devtools-mcp@latest
```

**注意：** 如果 8080 已被占用，可能需要换一个端口。

**第 3 步：** 在 Katalon Studio 中，用以下设置将服务器添加到 StudioAssist：

- **Connection URL:** `http://127.0.0.1:8080/mcp`
- **Transport type:** `HTTP`

连接后，Chrome DevTools MCP 工具即可在 StudioAssist 中使用。

</details>

<details>
  <summary>Mistral Vibe</summary>

在 ~/.vibe/config.toml 中添加：

```toml
[[mcp_servers]]
name = "chrome-devtools"
transport = "stdio"
command = "npx"
args = ["chrome-devtools-mcp@latest"]
```

</details>

<details>
  <summary>OpenCode</summary>

将以下配置添加到 `opencode.json` 文件。如果没有该文件，请在 `~/.config/opencode/opencode.json` 创建（<a href="https://opencode.ai/docs/mcp-servers">指南</a>）：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "chrome-devtools": {
      "type": "local",
      "command": ["npx", "-y", "chrome-devtools-mcp@latest"]
    }
  }
}
```

</details>

<details>
  <summary>Pi</summary>

使用 Pi CLI 安装 Chrome DevTools MCP 服务器（<a href="https://pi.dev/docs/latest/mcp">MCP 指南</a>）：

```bash
pi mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
pi mcp list
```

在运行中的会话里执行 `/reload` 以加载服务器。要添加 skills，请将[skill 文件夹](../skills)复制到 `~/.pi/agent/skills/`（<a href="https://pi.dev/docs/latest/skills">skills 指南</a>）。

</details>

<details>
  <summary>Qoder</summary>

在 **Qoder Settings** 中，前往 `MCP Server` > `+ Add` > 使用[标准配置][standard-config]。

或者，按照 <a href="https://docs.qoder.com/user-guide/chat/model-context-protocol">MCP 指南</a>操作，使用[标准配置][standard-config]。

</details>

<details>
  <summary>Qoder CLI</summary>

使用 Qoder CLI 安装 Chrome DevTools MCP 服务器（<a href="https://docs.qoder.com/cli/using-cli#mcp-servers">指南</a>）：

**项目范围：**

```bash
qodercli mcp add chrome-devtools -- npx chrome-devtools-mcp@latest
```

**全局：**

```bash
qodercli mcp add -s user chrome-devtools -- npx chrome-devtools-mcp@latest
```

</details>

<details>
  <summary>Visual Studio</summary>

**点击按钮安装：**

[<img src="https://img.shields.io