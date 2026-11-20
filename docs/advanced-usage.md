# 高级用法

## 并发会话

大多数 MCP 客户端会为每个对话启动一个 Chrome DevTools MCP 服务器。如果你的客户端在并发会话间共享单个服务器实例，页面 ID 路由默认启用。这会在页面级工具上暴露 `pageId`，让每个会话将工具调用路由到它正在操作的标签页。使用 `--no-page-id-routing` 可恢复仅选中页面的路由。

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

如果你运行多个独立的 MCP 客户端会话，并希望每个会话启动自己的临时 Chrome 配置文件，还需传入 `--isolated`。这可避免这些服务器实例之间共享默认的 Chrome DevTools MCP 用户数据目录。

## 用户数据目录

默认情况下，`chrome-devtools-mcp` 使用以下用户数据目录启动 Chrome 稳定版通道实例：

- Linux / macOS：`$HOME/.cache/chrome-devtools-mcp/chrome-profile`
- Windows：`%USERPROFILE%\.cache\chrome-devtools-mcp\chrome-profile`

对于非稳定版通道，通道名会附加到目录名后，例如 `chrome-profile-canary`。

用户数据目录在多次运行之间不会清除，并会复用于同一通道的后续运行。同一时间只能有一个浏览器使用它。将 `isolated` 选项设为 `true` 可改用临时用户数据目录，浏览器关闭后会自动清除。

## 自定义浏览器可执行文件

默认情况下，`chrome-devtools-mcp` 启动所选通道标准安装位置的 Chrome。要启动安装在自定义或非标准位置的基于 Chrome 的浏览器，请通过 `--executablePath`（或 `-e`）传入其可执行文件路径：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["chrome-devtools-mcp@latest", "--executablePath=/path/to/chrome"]
    }
  }
}
```

`--executablePath` 仅在 MCP 服务器启动浏览器时生效。它不能与 `--channel`、`--autoConnect`、`--browserUrl` 或 `--wsEndpoint` 组合使用。要使用已在运行的浏览器，请参阅[连接到运行中的 Chrome 实例](#connecting-to-a-running-chrome-instance)。

## 连接到运行中的 Chrome 实例

默认情况下，Chrome DevTools MCP 服务器会使用专用配置文件启动新的 Chrome 实例。这在某些情况下可能不理想：

- 如果你希望在手动站点测试和代理驱动测试之间切换时保持相同的应用状态。
- 当 MCP 需要登录网站时。某些账户在浏览器通过 WebDriver 控制时（Chrome DevTools MCP 服务器的默认启动机制）可能会阻止登录。
- 如果你在沙箱环境中运行 LLM，但希望连接到沙箱外运行的 Chrome 实例。

在这些情况下，请先启动 Chrome，再让 Chrome DevTools MCP 服务器连接到它。有两种方式：

- **自动连接（Chrome 144 可用）**：最适合在手动测试和代理驱动测试之间共享状态。
- **通过远程调试端口手动连接**：最适合在沙箱环境中运行。

### 自动连接到运行中的 Chrome 实例

**步骤 1：** 在 Chrome 中设置远程调试

在 Chrome（\>= M144）中，执行以下操作设置远程调试：

1.  导航到 `chrome://inspect/#remote-debugging` 启用远程调试。
2.  按照对话框 UI 允许或拒绝传入的调试连接。

**步骤 2：** 配置 Chrome DevTools MCP 服务器自动连接到运行中的 Chrome 实例

要将 `chrome-devtools-mcp` 服务器连接到运行中的 Chrome 实例，请为 MCP 服务器使用 `--autoConnect` 命令行参数。

以下代码片段是 gemini-cli 的示例配置：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["chrome-devtools-mcp@latest", "--autoConnect"]
    }
  }
}
```

**步骤 3：** 测试你的设置

确保浏览器正在运行。打开 gemini-cli 并运行以下提示：

```none
Check the performance of https://developers.chrome.com
```

> [!NOTE]
> <code>autoConnect</code> 选项需要用户启动 Chrome。如果用户有多个活动配置文件，MCP 服务器将连接到默认配置文件（由 Chrome 确定）。MCP 服务器可访问所选配置文件的所有打开窗口。

Chrome DevTools MCP 服务器将尝试连接到运行中的 Chrome 实例。它会显示对话框请求用户许可。

点击 **Allow** 后，Chrome DevTools MCP 服务器会打开 [developers.chrome.com](http://developers.chrome.com) 并采集性能跟踪。

### 使用端口转发手动连接

你可以使用 `--browser-url` 选项连接到运行中的 Chrome 实例。如果你在不允许启动新 Chrome 实例的沙箱环境中运行 MCP 服务器，这很有用。

以下是连接到运行中 Chrome 实例的分步指南：

**步骤 1：配置 MCP 客户端**

将 `--browser-url` 选项添加到 MCP 客户端配置。该选项的值应为运行中 Chrome 实例的 URL。`http://127.0.0.1:9222` 是常见默认值。

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": [
        "chrome-devtools-mcp@latest",
        "--browser-url=http://127.0.0.1:9222"
      ]
    }
  }
}
```

**步骤 2：启动 Chrome 浏览器**

> [!WARNING]
> 启用远程调试端口会在运行中的浏览器实例上开放调试端口。你机器上的任何应用都可连接此端口并控制浏览器。调试端口开放期间，请勿浏览任何敏感网站。

启用远程调试端口后启动 Chrome 浏览器。在启用调试端口启动新实例前，请确保关闭所有正在运行的 Chrome 实例。你选择的端口号必须与 MCP 客户端配置中 `--browser-url` 选项指定的端口号一致。

出于安全原因，[Chrome 要求启用远程调试端口时使用非默认用户数据目录](https://developer.chrome.com/blog/remote-debugging-port)。你可以使用 `--user-data-dir` 标志指定自定义目录。这可确保你的常规浏览配置文件和数据不会暴露给调试会话。

**macOS**

```bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222 --user-data-dir=/tmp/chrome-profile-stable
```

**Linux**

```bash
/usr/bin/google-chrome --remote-debugging-port=9222 --user-data-dir=/tmp/chrome-profile-stable
```

**Windows**

```bash
"C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222 --user-data-dir="%TEMP%\chrome-profile-stable"
```

**步骤 3：测试你的设置**

配置 MCP 客户端并启动 Chrome 浏览器后，可在 MCP 客户端中运行简单提示来测试设置：

```
Check the performance of https://developers.chrome.com
```

你的 MCP 客户端应连接到运行中的 Chrome 实例并收到性能报告。

如果遇到 VM 到主机的端口转发问题，请参阅 [`troubleshooting.md`](./troubleshooting.md#remote-debugging-between-virtual-machine-vm-and-host-fails) 中的“虚拟机（VM）与主机之间的远程调试失败”一节。

有关远程调试的更多细节，请参阅 [Chrome DevTools 文档](https://developer.chrome.com/docs/devtools/remote-debugging/)。

## 调试 Android 上的 Chrome

请参阅[这些说明](./debugging-android.md)。