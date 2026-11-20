# 配置

Chrome DevTools MCP 服务器支持以下配置选项：

<!-- BEGIN AUTO GENERATED OPTIONS -->

- **`--categoryInput`/ `--category-input`**
  设为 false 可排除与输入相关的工具。
  - **类型：** boolean
  - **默认值：** `true`

- **`--categoryNavigation`/ `--category-navigation`**
  设为 false 可排除与导航相关的工具。
  - **类型：** boolean
  - **默认值：** `true`

- **`--categoryEmulation`/ `--category-emulation`**
  设为 false 可排除与模拟相关的工具。
  - **类型：** boolean
  - **默认值：** `true`

- **`--categoryPerformance`/ `--category-performance`**
  设为 false 可排除与性能相关的工具。
  - **类型：** boolean
  - **默认值：** `true`

- **`--categoryNetwork`/ `--category-network`**
  设为 false 可排除与网络相关的工具。
  - **类型：** boolean
  - **默认值：** `true`

- **`--categoryNetworkMocks`/ `--category-network-mocks`**
  设为 true 可包含网络模拟工具。更改此选项需要重启服务器。
  - **类型：** boolean
  - **默认值：** `false`

- **`--categoryDebugging`/ `--category-debugging`**
  设为 false 可排除与调试相关的工具。
  - **类型：** boolean
  - **默认值：** `true`

- **`--categoryExtensions`/ `--category-extensions`**
  设为 true 可包含与扩展相关的工具。
  - **类型：** boolean
  - **默认值：** `false`

- **`--categoryExperimentalThirdParty`/ `--category-experimental-third-party`**
  设为 true 可启用被检查页面自身暴露的第三方开发者工具（通过 WebMCP 或 devtoolstooldiscovery）
  - **类型：** boolean
  - **默认值：** `false`

- **`--categoryMemory`/ `--category-memory`**
  设为 false 可排除与内存相关的工具。
  - **类型：** boolean
  - **默认值：** `true`

- **`--categoryExperimentalWebmcp`/ `--category-experimental-webmcp`**
  设为 true 可启用 WebMCP 工具（不包括调试工具，调试工具归在第三方开发者工具类别下）。需要 Chrome 150+ 并启用以下标志：`--enable-features=WebMCP`
  - **类型：** boolean
  - **默认值：** `false`

- **`--categoryPwa`/ `--category-pwa`**
  设为 true 可包含用于自动化渐进式 Web 应用（PWA）的工具（安装、启动、卸载和操作系统状态）。此功能仅支持管道连接；不支持 autoConnect、browserUrl 和 wsEndpoint。
  - **类型：** boolean
  - **默认值：** `false`

- **`--categoryWebauthn`/ `--category-webauthn`**
  设为 true 可包含管理虚拟 WebAuthn 认证器的工具，用于测试通行密钥和安全密钥流程。
  - **类型：** boolean
  - **默认值：** `false`

- **`--autoConnect`/ `--auto-connect`**
  如果指定，则自动连接到本地运行的浏览器（Chrome 144+），该浏览器使用由 channel 参数标识的用户数据目录（默认 channel 为 stable）。需要通过 chrome://inspect/#remote-debugging 在 Chrome 实例中启动远程调试服务器。
  - **类型：** boolean
  - **默认值：** `false`

- **`--browserUrl`/ `--browser-url`, `-u`**
  连接到正在运行且可调试的 Chrome 实例（例如 `http://127.0.0.1:9222`）。更多详情请参阅：https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/advanced-usage.md#connecting-to-a-running-chrome-instance。
  - **类型：** string

- **`--wsEndpoint`/ `--ws-endpoint`, `-w`**
  用于连接到正在运行的 Chrome 实例的 WebSocket 端点（例如 ws://127.0.0.1:9222/devtools/browser/<id>）。是 --browserUrl 的替代方案。
  - **类型：** string

- **`--wsHeaders`/ `--ws-headers`**
  WebSocket 连接的自定义请求头，JSON 格式（例如 '{"Authorization":"Bearer token"}'）。仅与 --wsEndpoint 配合使用。
  - **类型：** string

- **`--headless`**
  是否以无头（无 UI）模式运行。
  - **类型：** boolean
  - **默认值：** `false`

- **`--executablePath`/ `--executable-path`, `-e`**
  自定义 Chrome 可执行文件的路径。
  - **类型：** string

- **`--isolated`**
  如果指定，则创建一个临时 user-data-dir，浏览器关闭后自动清理。默认为 false。
  - **类型：** boolean
  - **默认值：** `false`

- **`--userDataDir`/ `--user-data-dir`**
  Chrome 用户数据目录的路径。默认为 $HOME/.cache/chrome-devtools-mcp/chrome-profile$CHANNEL_SUFFIX_IF_NON_STABLE
  - **类型：** string

- **`--channel`**
  指定要使用的其他 Chrome channel。默认为 stable channel 版本。
  - **类型：** string
  - **可选值：** `canary`、`dev`、`beta`、`stable`
  - **默认值：** `stable`

- **`--proxyServer`/ `--proxy-server`**
  Chrome 的代理服务器配置，启动浏览器时作为 --proxy-server 传入。详情请参阅 https://www.chromium.org/developers/design-documents/network-settings/。
  - **类型：** string

- **`--chromeArg`/ `--chrome-arg`**
  Chrome 的附加参数。仅在 chrome-devtools-mcp 启动 Chrome 时生效。
  - **类型：** array

- **`--ignoreDefaultChromeArg`/ `--ignore-default-chrome-arg`**
  显式禁用 Chrome 的默认参数。仅在 chrome-devtools-mcp 启动 Chrome 时生效。
  - **类型：** array

- **`--viewport`**
  服务器启动的 Chrome 实例的初始视口大小。例如 `1280x720`。在无头模式下，最大尺寸为 3840x2160px。
  - **类型：** string

- **`--acceptInsecureCerts`/ `--accept-insecure-certs`**
  如果启用，则忽略与自签名和过期证书相关的错误。请谨慎使用。
  - **类型：** boolean
  - **默认值：** `false`

- **`--blockedUrlPattern`/ `--blocked-url-pattern`**
  通过阻止指定的 URL 模式来限制浏览器的网络访问（使用 https://urlpattern.spec.whatwg.org/）。连接时会静默地从 URL 被阻止的目标上分离，并阻止运行时请求（包括导航和子资源）；通过 --watchConfig 更新模式时已打开的目标仍保持连接。接受模式数组。使用正则表达式组、命名组（`:name`）或部分 IPv4 通配符（例如 `(127\.\d+\.\d+\.\d+)`、`*://127.0.0.1::port/*` 或 `*://127.0.0.*/*`）的模式会被拒绝，因为它在重定向或子资源上不会被强制执行；请改用精确值或 `*` 通配符。模式遵循 URL Pattern 默认规则：省略端口时仅匹配该 scheme 的默认端口（例如 `https://internal.corp/*` 不匹配 `https://internal.corp:8443/`；要匹配任意端口请使用 `https://internal.corp:*/*`），且主机名必须完全匹配（带尾点的 `internal.corp.` 是不同的主机名）。
  - **类型：** array

- **`--allowedUrlPattern`/ `--allowed-url-pattern`**
  通过仅允许指定的 URL 模式来限制浏览器的网络访问（使用 https://urlpattern.spec.whatwg.org/）。需要 Chrome 149+。连接时会静默地从 URL 不被允许的目标上分离，并阻止运行时请求（包括导航和子资源）；通过 --watchConfig 更新模式时已打开的目标仍保持连接。接受模式数组。使用正则表达式组、命名组（`:name`）或部分 IPv4 通配符（例如 `(127\.\d+\.\d+\.\d+)`、`*://127.0.0.1::port/*` 或 `*://127.0.0.*/*`）的模式会被拒绝，因为它在重定向或子资源上不会被强制执行；请改用精确值或 `*` 通配符。模式遵循 URL Pattern 默认规则：省略端口时仅匹配该 scheme 的默认端口（例如 `https://internal.corp/*` 不匹配 `https://internal.corp:8443/`；要匹配任意端口请使用 `https://internal.corp:*/*`），且主机名必须完全匹配（带尾点的 `internal.corp.` 是不同的主机名）。
  - **类型：** array

- **`--screenshotFormat`/ `--screenshot-format`**
  当调用方未指定时，覆盖 take_screenshot 使用的默认输出格式。JPEG 和 WebP 比 PNG 小约 3-5 倍，可减少传输和存储大小。要减小上下文大小，请使用 --screenshotMaxWidth / --screenshotMaxHeight，因为图像 token 随尺寸而非编码字节数增长。未设置时保留现有默认值（"png"）。
  - **类型：** string
  - **可选值：** `jpeg`、`png`、`webp`
  - **默认值：** `png`

- **`--screenshotQuality`/ `--screenshot-quality`**
  当调用方未指定时，覆盖 take_screenshot 对 JPEG 和 WebP 使用的默认压缩质量（0-100）。值越低文件越小。对 PNG 无效。未设置时保留 Puppeteer 默认值。
  - **类型：** number

- **`--screenshotMaxWidth`/ `--screenshot-max-width`**
  截图的最大宽度（像素）。如果捕获的图像更宽，则在返回前按比例缩小（保持宽高比）。可减小 AI 对话中的上下文大小。未设置表示不调整大小。
  - **类型：** number

- **`--screenshotMaxHeight`/ `--screenshot-max-height`**
  截图的最大高度（像素）。如果捕获的图像更高，则在返回前按比例缩小（保持宽高比）。可与 --screenshot-max-width 组合使用；取较小的缩放因子。未设置表示不调整大小。
  - **类型：** number

- **`--logFile`/ `--log-file`**
  写入调试日志的文件路径。将环境变量 `NODE_DEBUG` 设为 `*` 可启用详细日志。适用于提交 bug 报告。
  - **类型：** string

- **`--pageIdRouting`/ `--page-id-routing`**
  要求页面级工具提供 pageId，并按页面 ID 路由请求（适用于并发代理会话）。使用 --no-page-id-routing 可禁用。
  - **类型：** boolean
  - **默认值：** `true`

- **`--experimentalDevtools`/ `--experimental-devtools`**
  是否启用对 DevTools 目标的自动化。这会将 DevTools UI 暴露给输入工具，包括可在被检查页面中执行 JavaScript 的 Console。--no-javascript-evaluation 不会限制此路径。
  - **类型：** boolean
  - **默认值：** `false`

- **`--experimentalVision`/ `--experimental-vision`**
  是否启用基于坐标的工具，例如 click_at(x,y)。通常需要能够通过查看截图生成准确坐标的 computer-use 模型。
  - **类型：** boolean
  - **默认值：** `false`

- **`--memoryDebugging`/ `--memory-debugging`, `--experimentalMemory`**
  是否启用内存调试工具。
  - **类型：** boolean
  - **默认值：** `false`

- **`--experimentalStructuredContent`/ `--experimental-structured-content`**
  是否输出结构化格式的内容。
  - **类型：** boolean
  - **默认值：** `false`

- **`--experimentalIncludeAllPages`/ `--experimental-include-all-pages`**
  是否将 webview 或后台页面等各类页面也作为页面包含。
  - **类型：** boolean
  - **默认值：** `false`

- **`--experimentalScreencast`/ `--experimental-screencast`**
  使用基于 ffmpeg 的旧版录屏实现，而非内置的 Chrome 录制器。请安装 ffmpeg https://www.ffmpeg.org/download.html 并确保其在 MCP 服务器的 PATH 中可用。此选项已弃用，将被默认提供的无依赖录屏命令取代。
  - **类型：** boolean
  - **默认值：** `false`

- **`--experimentalFfmpegPath`/ `--experimental-ffmpeg-path`**
  旧版录屏录制所用的 ffmpeg 可执行文件路径（需要 --experimentalScreencast）。
  - **类型：** string

- **`--screencastFps`/ `--screencast-fps`, `--experimentalScreencastFps`**
  录屏录制使用的帧率。较低的值可减小文件大小和编码负载。
  - **类型：** number

- **`--performanceCrux`/ `--performance-crux`**
  设为 false 可禁止将性能追踪中的 URL 发送到 CrUX API 以获取实测性能数据。
  - **类型：** boolean
  - **默认值：** `true`

- **`--usageStatistics`/ `--usage-statistics`**
  设为 false 可选择退出使用统计收集。Google 收集使用数据以改进工具，依据 Google 隐私政策（https://policies.google.com/privacy）处理。这与 Chrome 浏览器指标无关。如果设置了 `CHROME_DEVTOOLS_MCP_NO_USAGE_STATISTICS` 或 `CI` 环境变量，则禁用。
  - **类型：** boolean
  - **默认值：** `true`

- **`--javascriptEvaluation`/ `--javascript-evaluation`**
  设为 false 可禁用服务器中用于执行调用方提供的 JavaScript 的入口：评估工具（evaluate_script、set_logpoints 和 slim evaluate）被禁用，navigate_page 中的 initScript 参数被关闭，且不允许导航到 javascript:、data: 或 vbscript: URL。这不是 JavaScript 沙箱：页面脚本以及通过其他允许的浏览器交互（例如点击或输入）触发的 JavaScript 仍会运行。如果同时启用了 --experimentalDevtools，则 DevTools UI（包括在被检查页面中执行代码的 Console）可被自动化，且不受此选项限制。客户端仍负责验证工具调用和参数。
  - **类型：** boolean
  - **默认值：** `true`

- **`--fileNavigations`/ `--file-navigations`**
  设为 false 可禁止导航到 file: URL。禁用后，new_page、navigate_page、slim navigate 工具、install_pwa 和 launch_pwa 会拒绝 file: URL，包括指向它们的 view-source: URL。这限制的是服务器执行的导航。它不是文件系统沙箱：不影响服务器连接时浏览器已打开的页面，且浏览器仍可通过其他方式访问文件系统。要完全限制文件系统访问，请使用操作系统沙箱。
  - **类型：** boolean
  - **默认值：** `true`

- **`--backgroundNewPages`/ `--background-new-pages`**
  设为 true 可在后台打开 new_page 创建的每个页面，不将其置于前台，无论工具调用传入的 background 参数为何。当多个代理或用户共享一个可见浏览器且新标签页不应抢占焦点时很有用。
  - **类型：** boolean
  - **默认值：** `false`

- **`--sourceMaps`/ `--source-maps`**
  是否在 DevTools 中启用 source map。使用 --no-source-maps 可禁用。
  - **类型：** boolean
  - **默认值：** `true`

- **`--maxNavigationSaved`/ `--max-navigation-saved`**
  每个页面保留网络请求和控制台消息的导航次数（包括当前导航）。必须在 1 到 25 之间。对于更早的导航，会保留请求 URL、状态码以及请求和响应头，足以追踪重定向跳转，但响应体通常不再可用。
  - **类型：** number
  - **默认值：** `3`

- **`--slim`**
  仅暴露 3 个"精简"工具，涵盖导航、脚本执行和截图。适用于基本浏览器任务。
  - **类型：** boolean
  - **默认值：** `false`

- **`--redactNetworkHeaders`/ `--redact-network-headers`**
  如果为 true，则在返回给客户端前对部分被视为敏感的网络请求头进行脱敏。
  - **类型：** boolean
  - **默认值：** `false`

- **`--allowUnrestrictedPaths`/ `--allow-unrestricted-paths`**
  如果设置，则禁用当 MCP 客户端未协商 roots 能力时适用的默认路径限制。默认情况下，当未配置 roots 时，读写本地文件的工具被限制在操作系统临时目录内。仅在连接未实现 MCP roots 且需要访问临时目录之外路径的可信本地客户端时使用。
  - **类型：** boolean
  - **默认值：** `false`

- **`--filesystemRoot`/ `--filesystem-root`, `--workspace`**
  文件系统工具允许访问的目录。可多次指定。
  - **类型：** array
  - **默认值：** `操作系统临时目录`

- **`--config`**
  JSON 配置文件的路径。
  - **类型：** string

- **`--