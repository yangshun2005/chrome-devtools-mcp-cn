# 故障排查

## 通用建议

- 运行 `npx chrome-devtools-mcp@latest --help`，测试 MCP 服务器能否在你的机器上运行。
- 确保 MCP 客户端使用的 npm 和 node 版本与终端一致。
- 配置 MCP 客户端时，尝试给 `npx` 加上 `--yes` 参数，自动接受安装提示。
- 在 `chrome-devtools-mcp` 服务器的输出中查找具体错误。通常如果客户端是 IDE，日志会显示在输出面板中。
- 在 [GitHub 仓库的 Issue 和讨论](https://github.com/ChromeDevTools/chrome-devtools-mcp)中搜索帮助或已有的类似问题。

## 调试

启用调试并指定日志文件来启动 MCP 服务器：

- `NODE_DEBUG=* npx chrome-devtools-mcp@latest --log-file=/path/to/chrome-devtools-mcp.log`

使用客户端时，可通过 `.mcp.json` 进行调试：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--log-file",
        "/path/to/chrome-devtools-mcp.log"
      ],
      "env": {
        "NODE_DEBUG": "*"
      }
    }
  }
}
```

## 具体问题

### `Error [ERR_MODULE_NOT_FOUND]: Cannot find module ...`

通常表示使用了不受支持的 Node 版本，或 `npm`/`npx` 缓存已损坏。尝试清除缓存、卸载 `chrome-devtools-mcp` 后重新安装。清除缓存：

```sh
rm -rf ~/.npm/_npx # NOTE: this might remove other installed npx executables.
npm cache clean --force
```

### `Target closed` 错误

表示浏览器无法启动。确保没有正在运行的 Chrome 实例，或将其关闭。确保已安装最新的稳定版 Chrome，且[你的系统能够运行 Chrome](https://support.google.com/chrome/a/answer/7100626?hl=en)。

### macOS 上使用 Web Bluetooth 时 Chrome 崩溃

在 macOS 上，由 MCP 客户端应用（如 Claude Desktop）启动的 Chrome 在出现 Web Bluetooth 提示时可能崩溃。这是由 macOS 隐私权限违规（TCC）导致的。

解决方法：在 `系统设置 > 隐私与安全性 > 蓝牙` 中为 MCP 客户端应用授予蓝牙权限。授权后重启客户端应用并开启新的 MCP 会话。

### macOS 上 `--autoConnect` 失败，报 `Could not find DevToolsActivePort` 或 `EPERM: operation not permitted`

在较新的 macOS 版本中，macOS 会阻止其他应用读取 `~/Library/Application Support/Google/Chrome` 下的浏览器配置文件目录，且不显示权限提示（TCC `kTCCServiceSystemPolicyAppDataDetailed`）。由于 MCP 服务器继承启动它的终端或 IDE 宿主应用的权限，读取 `DevToolsActivePort` 会失败并报 `EPERM: operation not permitted`。

解决方法：在 `系统设置 > 隐私与安全性 > 文件与文件夹` 中，为终端或 MCP 客户端宿主应用授予访问 **Google Chrome** 的权限。更改立即生效，无需重启终端或 MCP 客户端。

### 虚拟机（VM）与宿主机之间的远程调试失败

从虚拟机（VM）内连接宿主机上运行的 Chrome 时，Chrome 可能因 'Host' 头校验而拒绝连接。可从 VM 到宿主机建立 SSH 隧道绕过此限制。在 VM 中运行：

```sh
ssh -N -L 127.0.0.1:9222:127.0.0.1:9222 <user>@<host-ip>
```

将 VM 内的 MCP 连接指向 `http://127.0.0.1:9222`。这样 DevTools 就能访问宿主机浏览器，而不会触发 Host 校验错误。

### 操作系统沙箱

部分 MCP 客户端允许使用 macOS Seatbelt 或 Linux 容器对 MCP 服务器进行沙箱隔离。启用沙箱后，`chrome-devtools-mcp` 无法启动 Chrome，因为 Chrome 需要权限来创建自己的沙箱。变通方法：在 MCP 客户端中为 `chrome-devtools-mcp` 禁用沙箱，或使用 `--browser-url` 连接你在 MCP 客户端沙箱之外手动启动的 Chrome 实例。

### 以 root 身份运行

Chrome 不能以 root 身份启动（[crbug.com/638180](https://crbug.com/638180)）。它会立即退出，`chrome-devtools-mcp` 会报告 Chrome 启动失败。这在以 root 运行所有内容的容器和 CI 镜像中很常见。

以非 root 用户运行 `chrome-devtools-mcp`。在容器中，在镜像里创建非特权用户并用 `USER` 切换；构建本身仍可以 root 运行。Chrome 沙箱所需的宿主机侧设置，参见 Puppeteer 的 [Setting up Chrome Linux sandbox](https://pptr.dev/troubleshooting#setting-up-chrome-linux-sandbox)。

### WSL

默认情况下，WSL 中的 `chrome-devtools-mcp` 要求 Chrome 安装在 Linux 环境中。虽然它通常会尝试启动 Windows 侧的 Chrome，但由于[已知的 WSL 问题](https://github.com/microsoft/WSL/issues/14201)，目前会失败。请确保使用[与 Chrome 兼容的 Linux 发行版](https://support.google.com/chrome/a/answer/7100626)。

可用的变通方法：

- **在 WSL 中安装 Google Chrome：**
  - `wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb`
  - `sudo dpkg -i google-chrome-stable_current_amd64.deb`

- **使用镜像网络：**
  1. 配置 [WSL 的镜像网络](https://learn.microsoft.com/en-us/windows/wsl/networking)。
  2. 在 Windows 侧启动 Chrome：
     `chrome.exe --remote-debugging-port=9222 --user-data-dir=C:\path\to\dir`
  3. 启动 `chrome-devtools-mcp`：
     `npx chrome-devtools-mcp --browser-url http://127.0.0.1:9222`

- **使用 PowerShell 或 Git Bash** 代替 WSL。

### Windows 10：MCP 服务器 'chrome-devtools' 发现期间出错：MCP error -32000: Connection closed

- **方案 1** 使用 `cmd` 调用（更多信息见 https://github.com/modelcontextprotocol/servers/issues/1082#issuecomment-2791786310）

  ```json
  "mcpServers": {
      "chrome-devtools": {
        "command": "cmd",
        "args": ["/c", "npx", "-y", "chrome-devtools-mcp@latest"]
      }
    }
  ```

  > **关键改动：** 在 Windows 上，通过 `npx` 运行 Node.js 包时，通常需要加 `cmd /c` 前缀，才能从 VSCode 扩展宿主等其他进程中正确执行。因此，将 `"command": "npx"` 替换为 `"command": "cmd"`，并把实际的 `npx` 命令移入 `"args"` 数组，前面加上 `"/c"`。此修复让 Windows 能正确解析命令并启动服务器。

- **方案 2** 不额外套一层 shell，直接写 `npx` 的绝对路径：
  > 注意：以下路径仅为示例。你必须根据机器上 `npx` 的实际位置进行调整。视环境不同，文件扩展名可能是 `.cmd`、`.bat` 或 `.exe`，而非 `.ps1`。另外，JSON 格式要求使用双反斜杠（`\\`）作为路径分隔符。
  ```json
  "mcpServers": {
      "chrome-devtools": {
        "command": "C:\\nvm4w\\nodejs\\npx.ps1",
        "args": ["-y", "chrome-devtools-mcp@latest"]
      }
    }
  ```

### Claude Code 插件安装失败，报 `Failed to clone repository`

将 `chrome-devtools-mcp` 作为 Claude Code 插件安装时（无论从官方市场还是通过 `/plugin marketplace add`），如果环境无法通过 443 端口（HTTPS）访问 `github.com`，安装可能因超时失败：

```
Failed to download/cache plugin chrome-devtools-mcp: Failed to clone repository:
  Cloning into '...'...
  fatal: unable to access 'https://github.com/ChromeDevTools/chrome-devtools-mcp.git/':
  Failed to connect to github.com port 443
```

这可能发生在出站 HTTPS 连接受限、有企业防火墙，或代理配置阻止 HTTPS git 操作的环境中。

**变通方法 1：用 SSH 代替 HTTPS**

如果已配置 GitHub 的 SSH 访问，可将所有 GitHub HTTPS URL 重定向到 SSH：

```sh
git config --global url."git@github.com:".insteadOf "https://github.com/"
```

然后重试插件安装。这会让 git 对所有 GitHub 操作使用你的 SSH 密钥而非 HTTPS。

**变通方法 2：改用 CLI 安装**

如果插件市场方式失败，可以不克隆仓库，直接将 `chrome-devtools-mcp` 安装为 MCP 服务器：

```sh
claude mcp add chrome-devtools --scope user npx chrome-devtools-mcp@latest
```

这完全绕过 git clone，改用 npm/npx 获取包。注意此方法只安装 MCP 服务器，不含捆绑的 skills。

### 使用 `--autoConnect` 时连接超时

如果使用 `--autoConnect` 标志，且 `list_pages`、`new_page` 或 `navigate_page` 等工具超时失败（如 `ProtocolError: Network.enable timed out` 或 `The socket connection was closed unexpectedly`），通常表示 MCP 服务器无法与运行中的 Chrome 实例正确握手。请确保：

1. Chrome 144+ **已**在运行。
2. 已通过 `chrome://inspect/#remote-debugging` 在 Chrome 中启用远程调试。
3. 已在浏览器中允许远程调试连接提示。
4. 没有其他 MCP 服务器或工具尝试连接同一调试端口。

> [!IMPORTANT]
> 在 Chrome 149 及更早版本中，连接问题可能由冻结或未加载的标签页引起。
> Chrome DevTools MCP 会强制加载所有标签页，因此请确保系统资源充足。
> 目前不建议在运行数百个标签页的浏览器实例上使用 Chrome DevTools MCP。
> 详见 [Issue #1921](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/1921)。