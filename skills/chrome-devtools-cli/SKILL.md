---
name: chrome-devtools-cli
description: 使用此技能编写 shell 脚本或运行 shell 命令，在浏览器中自动化任务，或通过 CLI 使用 Chrome DevTools。
---

`chrome-devtools-mcp` CLI 让你可以从终端与浏览器交互。

## 安装配置

_注意：如果你是首次使用 CLI，请参阅 [references/installation.md](references/installation.md) 进行配置。安装是一次性前置步骤，**不属于**常规 AI 工作流。_

## AI 工作流

1. **执行**：直接运行工具。如果不知道目标页面的 ID，运行 `chrome-devtools list_pages` 查找。后台服务器会隐式启动；**不要**在每次使用前运行 `start`/`status`/`stop`。
2. **检查**：使用 `chrome-devtools take_snapshot <pageId>` 获取元素的 `<uid>`。
3. **操作**：使用 `chrome-devtools click <pageId> <uid>`、`chrome-devtools fill <pageId> <uid> <value>` 等。状态在命令之间保持。

快照示例：

```
uid=1_0 RootWebArea "Example Domain" url="https://example.com/"
  uid=1_1 heading "Example Domain" level="1"
```

## 权限与文件访问

默认情况下，CLI 拥有完整的文件系统访问权限（`--allowUnrestrictedPaths=true`），允许文件保存参数（`--filePath`、`--outputDirPath`）和 `upload_file` 访问系统任意位置的文件。如需将文件访问限制在操作系统临时目录，请传入 `--allowUnrestrictedPaths=false`。

## 命令用法

```sh
chrome-devtools <tool> [arguments] [flags]
```

- 必需参数按位置传递；可选参数使用标志。
- 对任意命令使用 `--help` 查看用法详情。
- 输出默认为类似 Markdown 的纯文本；传入 `--output-format=json` 获取 JSON。

## 输入自动化（`<uid>` 来自快照）

```bash
chrome-devtools take_snapshot 1 # 获取页面文本快照以获取元素的 UID
chrome-devtools click 1 "id" # 点击指定元素
chrome-devtools click 1 "id" --dblClick true --includeSnapshot true # 双击并返回快照
chrome-devtools drag 1 "src" "dst" # 将一个元素拖拽到另一个元素上
chrome-devtools drag 1 "src" "dst" --includeSnapshot true # 拖拽元素并返回快照
chrome-devtools fill 1 "id" "text" # 在输入框、文本域中输入文本，或选择选项
chrome-devtools fill 1 "id" "text" --includeSnapshot true # 填充元素并返回快照
chrome-devtools handle_dialog 1 accept # 处理浏览器对话框（accept/dismiss）
chrome-devtools handle_dialog 1 dismiss --promptText "hi" # 关闭对话框并输入提示文本
chrome-devtools hover 1 "id" # 悬停在指定元素上
chrome-devtools hover 1 "id" --includeSnapshot true # 悬停元素并返回快照
chrome-devtools press_key 1 "Enter" # 按下按键或组合键（"Control+A"、"Escape"）
chrome-devtools press_key 1 "Control+A" --includeSnapshot true # 按键并返回快照
chrome-devtools type_text 1 "hello" # 使用键盘向聚焦的输入框输入文本
chrome-devtools type_text 1 "hello" --submitKey "Enter" # 输入文本并按下提交键
chrome-devtools upload_file 1 "id" "file.txt" # 通过指定元素上传文件
chrome-devtools upload_file 1 "id" "file.txt" --includeSnapshot true # 上传文件并返回快照
```

## 导航

```bash
chrome-devtools close_page 1 # 按索引关闭页面
chrome-devtools list_pages # 获取浏览器中打开的页面列表
chrome-devtools navigate_page 1 --url "https://example.com" # 将当前选中的页面导航到 URL
chrome-devtools navigate_page 1 --type "reload" --ignoreCache true # 重新加载页面并忽略缓存
chrome-devtools navigate_page 1 --url "https://example.com" --timeout 5000 # 带超时导航
chrome-devtools navigate_page 1 --type "reload" --handleBeforeUnload "accept" # 处理 before unload 对话框
chrome-devtools navigate_page 1 --type "back" --initScript "foo()" # 后退并运行初始化脚本
chrome-devtools new_page "https://example.com" # 创建新页面
chrome-devtools new_page "https://example.com" --background true --timeout 5000 # 在后台创建新页面
chrome-devtools new_page "https://example.com" --isolatedContext "ctx" # 创建带隔离上下文的新页面
chrome-devtools select_page 1 # 选择页面作为后续工具调用的上下文
chrome-devtools select_page 1 --bringToFront true # 选择页面并置于前台
```

## 模拟

每次 `emulate` 调用都会替换之前配置的所有模拟设置（`--extraHttpHeaders` 除外），因此请在单次调用中传入所有需要的设置。

```bash
chrome-devtools emulate 1 --networkConditions "Offline" # 模拟网络条件（重置其他设置）
chrome-devtools emulate 1 --cpuThrottlingRate 4 --geolocation "0,0" # 模拟 CPU 节流和地理位置（重置其他设置）
chrome-devtools emulate 1 --colorScheme "dark" --viewport "1920x1080" # 模拟配色方案和视口（重置其他设置）
chrome-devtools emulate 1 --userAgent "Mozilla/5.0..." # 模拟用户代理（重置其他设置）
chrome-devtools resize_page 1 1920 1080 # 调整选中页面的窗口大小
```

## 性能

```bash
chrome-devtools performance_analyze_insight 1 "NAVIGATION_0" "LCPBreakdown" # 获取特定性能洞察的详细信息（pageId、insightSetId、insightName）
chrome-devtools performance_start_trace 1 --reload true --autoStop false # 开始性能跟踪记录（reload、autoStop）
chrome-devtools performance_start_trace 1 --reload true --autoStop true --filePath "t.json.gz" # 开始跟踪并保存到文件
chrome-devtools performance_stop_trace 1 # 停止活动的性能跟踪
chrome-devtools performance_stop_trace 1 --filePath "t.json.gz" # 停止跟踪并保存到文件
```

## 内存

```bash
chrome-devtools take_heapsnapshot 1 "./snap.heapsnapshot" # 捕获内存堆快照
```

### 内存调试（需要 `--memoryDebugging=true`）

```bash
chrome-devtools get_heapsnapshot_summary "./snap.heapsnapshot" # 获取快照摘要统计
chrome-devtools compare_heapsnapshots "./base.heapsnapshot" "./target.heapsnapshot" # 比较两个快照
chrome-devtools get_heapsnapshot_details "./snap.heapsnapshot" # 快照统计和按类聚合（含类 ID）
chrome-devtools get_heapsnapshot_class_nodes "./snap.heapsnapshot" 18 # 检查类的实例（类 ID 来自 get_heapsnapshot_details）
chrome-devtools get_heapsnapshot_dominators "./snap.heapsnapshot" 123 # 节点的支配树
chrome-devtools get_heapsnapshot_duplicate_strings "./snap.heapsnapshot" # 查找重复字符串
chrome-devtools get_heapsnapshot_edges "./snap.heapsnapshot" 123 # 节点的边/引用
chrome-devtools get_heapsnapshot_object_details "./snap.heapsnapshot" 123 # 按节点 ID 获取对象详情
chrome-devtools get_heapsnapshot_retainers "./snap.heapsnapshot" 123 # 保留对象
chrome-devtools get_heapsnapshot_retaining_paths "./snap.heapsnapshot" 123 # 最短保留路径
chrome-devtools close_heapsnapshot "./snap.heapsnapshot" # 释放已加载快照的内存
```

## 网络

网络模拟命令需要在服务器启动时传入 `--categoryNetworkMocks=true`。
更改此标志需要重启服务器。

```bash
chrome-devtools get_network_request 1 # 获取页面 1 当前选中的网络请求
chrome-devtools get_network_request 1 --reqid 1 --requestFilePath "req.md" # 按 ID 获取请求并保存到文件
chrome-devtools get_network_request 1 --reqid 1 --responseFilePath "res.md" # 将响应体保存到文件
chrome-devtools list_network_requests 1 # 列出页面 1 的所有网络请求
chrome-devtools list_network_requests 1 --pageSize 50 --pageIdx 0 # 分页列出网络请求
chrome-devtools list_network_requests 1 --resourceTypes fetch # 按资源类型过滤请求
chrome-devtools list_network_requests 1 --includePreservedRequests true # 包含保留的请求
chrome-devtools mock_network 1 --route 'https://example.com/app.js*' \
  --body /tmp/app.js # 用本地文件替换响应
chrome-devtools mock_network 1 --route 'https://example.com/app.js*' \
  --upstream https://example.com/dev.js # 改用上游 URL
chrome-devtools mock_network 1 # 查看所有模拟
chrome-devtools mock_network 1 --resourceType script # 查看脚本模拟
chrome-devtools remove_network_mock 1 --route 'https://example.com/app.js*'
chrome-devtools remove_network_mock 1 # 清除页面 1 的所有模拟
```

## 调试与检查

```bash
chrome-devtools evaluate_script "() => document.title" --pageId 1 # 在页面 1 上执行 JavaScript 函数
chrome-devtools evaluate_script "(a) => a.innerText" --pageId 1 --args 1_4 # 在页面 1 上执行带 UID 参数的 JS
chrome-devtools evaluate_script --pageId 1 --sourcePath /path/to/script.js --format script # 在页面 1 上执行本地经典 JavaScript 文件
chrome-devtools get_console_message 1 1 # 按 ID 获取控制台消息
chrome-devtools get_css_styles 1 "1_4" # 在页面 1 上分页获取 CSS 样式（默认：10 条规则，pageIdx 0）
chrome-devtools get_css_styles 1 "1_4" --pageSize 20 --pageIdx 1 # 使用自定义页大小和从 0 开始的页索引分页 CSS 规则
chrome-devtools lighthouse_audit 1 --mode "navigation" # 运行 Lighthouse 导航审计
chrome-devtools lighthouse_audit 1 --mode "snapshot" --device "mobile" # 在移动端运行 Lighthouse 快照审计
chrome-devtools lighthouse_audit 1 --outputDirPath ./out # 运行 Lighthouse 审计并保存报告
chrome-devtools list_console_messages 1 # 列出所有控制台消息
chrome-devtools list_console_messages 1 --pageSize 20 --pageIdx 1 # 分页列出控制台消息
chrome-devtools list_console_messages 1 --types error --types info # 按类型过滤控制台消息
chrome-devtools list_console_messages 1 --includePreservedMessages true # 包含保留的消息
chrome-devtools take_screenshot 1 # 截取页面视口截图
chrome-devtools take_screenshot 1 --fullPage true --format "jpeg" --quality 80 # 截取全页 JPEG 截图并指定质量
chrome-devtools take_screenshot 1 --uid "id" --filePath "s.png" # 截取元素截图
chrome-devtools take_snapshot 1 # 从 a11y 树获取页面文本快照
chrome-devtools take_snapshot 1 --verbose true --filePath "s.txt" # 获取详细快照并保存到文件
chrome-devtools screencast_start 1 --filePath "screen.mp4" # 在页面 1 上开始 mp4 屏幕录制
chrome-devtools screencast_stop 1 # 停止页面 1 上的活动屏幕录制
```

## 扩展

```bash
chrome-devtools list_extensions # 列出浏览器中加载的未打包 Chrome 扩展
chrome-devtools install_extension "/path/to/extension" # 从指定路径安装 Chrome 扩展
chrome-devtools uninstall_extension "extension_id" # 按 ID 卸载 Chrome 扩展
chrome-devtools reload_extension "extension_id" # 按 ID 重新加载未打包的 Chrome 扩展
chrome-devtools trigger_extension_action "extension_id" # 按 ID 触发扩展的默认操作
```

## 渐进式 Web 应用（需要 `--categoryPwa=true`）

```bash
chrome-devtools install_pwa "https://example.com/" "https://example.com/" # 按 manifest ID 和安装 URL 安装 PWA
chrome-devtools launch_pwa "https://example.com/" # 启动已安装的 PWA
chrome-devtools get_os_app_state "https://example.com/" # 获取操作系统应用安装状态
chrome-devtools uninstall_pwa "https://example.com/" # 卸载 PWA 并关闭窗口
```

## 实验性功能

实验性工具默认禁用。在 `start` 时使用相应标志启用。

```bash
chrome-devtools click_at 1 100 200 # 在页面 1 的指定坐标点击（需要 --experimentalVision=true）
chrome-devtools list_webmcp_tools 1 # 列出页面 1 上的所有 WebMCP 工具（需要 --categoryExperimentalWebmcp=true）
chrome-devtools execute_webmcp_tool 1 "tool_name" --input '{"arg":"val"}' # 在页面 1 上执行 WebMCP 工具（需要 --categoryExperimentalWebmcp=true）
chrome-devtools list_3p_developer_tools 1 # 列出页面 1 上的第三方开发者工具（需要 --categoryExperimentalThirdParty=true）
chrome-devtools execute_3p_developer_tool 1 "tool_name" --params '{"arg":"val"}' # 在页面 1 上执行第三方开发者工具（需要 --categoryExperimentalThirdParty=true）
```

## 服务管理

```bash
chrome-devtools start   # 启动或重启 chrome-devtools-mcp
chrome-devtools start --headless=false # 以可见浏览器窗口启动
chrome-devtools status  # 检查 chrome-devtools-mcp 是否正在运行
chrome-devtools stop    # 停止 chrome-devtools-mcp（如有运行）
```