<!-- AUTO GENERATED DO NOT EDIT - run 'npm run gen' to update-->

# Chrome DevTools MCP 工具参考

- **[输入自动化](#input-automation)**（10 个工具）
  - [`click`](#click)
  - [`drag`](#drag)
  - [`fill`](#fill)
  - [`fill_form`](#fill_form)
  - [`handle_dialog`](#handle_dialog)
  - [`hover`](#hover)
  - [`press_key`](#press_key)
  - [`type_text`](#type_text)
  - [`upload_file`](#upload_file)
  - [`click_at`](#click_at)
- **[导航自动化](#navigation-automation)**（6 个工具）
  - [`close_page`](#close_page)
  - [`list_pages`](#list_pages)
  - [`navigate_page`](#navigate_page)
  - [`new_page`](#new_page)
  - [`select_page`](#select_page)
  - [`wait_for`](#wait_for)
- **[模拟](#emulation)**（2 个工具）
  - [`emulate`](#emulate)
  - [`resize_page`](#resize_page)
- **[性能](#performance)**（3 个工具）
  - [`performance_analyze_insight`](#performance_analyze_insight)
  - [`performance_start_trace`](#performance_start_trace)
  - [`performance_stop_trace`](#performance_stop_trace)
- **[网络](#network)**（2 个工具）
  - [`get_network_request`](#get_network_request)
  - [`list_network_requests`](#list_network_requests)
- **[调试](#debugging)**（10 个工具）
  - [`evaluate_script`](#evaluate_script)
  - [`get_console_message`](#get_console_message)
  - [`get_css_styles`](#get_css_styles)
  - [`lighthouse_audit`](#lighthouse_audit)
  - [`list_console_messages`](#list_console_messages)
  - [`screencast_start`](#screencast_start)
  - [`screencast_stop`](#screencast_stop)
  - [`set_logpoints`](#set_logpoints)
  - [`take_screenshot`](#take_screenshot)
  - [`take_snapshot`](#take_snapshot)
- **[内存](#memory)**（14 个工具）
  - [`take_heapsnapshot`](#take_heapsnapshot)
  - [`analyze_heapsnapshot_contexts`](#analyze_heapsnapshot_contexts)
  - [`close_heapsnapshot`](#close_heapsnapshot)
  - [`compare_heapsnapshots`](#compare_heapsnapshots)
  - [`get_heapsnapshot_class_nodes`](#get_heapsnapshot_class_nodes)
  - [`get_heapsnapshot_details`](#get_heapsnapshot_details)
  - [`get_heapsnapshot_dominators`](#get_heapsnapshot_dominators)
  - [`get_heapsnapshot_duplicate_strings`](#get_heapsnapshot_duplicate_strings)
  - [`get_heapsnapshot_edges`](#get_heapsnapshot_edges)
  - [`get_heapsnapshot_object_details`](#get_heapsnapshot_object_details)
  - [`get_heapsnapshot_retainers`](#get_heapsnapshot_retainers)
  - [`get_heapsnapshot_retaining_paths`](#get_heapsnapshot_retaining_paths)
  - [`get_heapsnapshot_summary`](#get_heapsnapshot_summary)
  - [`query_heapsnapshot_objects`](#query_heapsnapshot_objects)
- **[网络 Mock](#network-mocks)**（2 个工具）
  - [`mock_network`](#mock_network)
  - [`remove_network_mock`](#remove_network_mock)
- **[扩展](#extensions)**（5 个工具）
  - [`install_extension`](#install_extension)
  - [`list_extensions`](#list_extensions)
  - [`reload_extension`](#reload_extension)
  - [`trigger_extension_action`](#trigger_extension_action)
  - [`uninstall_extension`](#uninstall_extension)
- **[第三方](#third-party)**（2 个工具）
  - [`execute_3p_developer_tool`](#execute_3p_developer_tool)
  - [`list_3p_developer_tools`](#list_3p_developer_tools)
- **[WebMCP](#webmcp)**（2 个工具）
  - [`execute_webmcp_tool`](#execute_webmcp_tool)
  - [`list_webmcp_tools`](#list_webmcp_tools)
- **[渐进式 Web 应用](#progressive-web-apps)**（4 个工具）
  - [`get_os_app_state`](#get_os_app_state)
  - [`install_pwa`](#install_pwa)
  - [`launch_pwa`](#launch_pwa)
  - [`uninstall_pwa`](#uninstall_pwa)
- **[WebAuthn](#webauthn)**（1 个工具）
  - [`configure_webauthn`](#configure_webauthn)

## 输入自动化

### `click`

**描述：** 点击指定元素

**参数：**

- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **uid**（string）**（必填）**：页面内容快照中该元素的 uid
- **dblClick**（boolean）*（可选）*：设为 true 表示双击。默认 false。
- **includeSnapshot**（boolean）*（可选）*：是否在响应中包含快照。默认 false。

---

### `drag`

**描述：** 将元素[`拖拽`](#drag)到另一个元素上

**参数：**

- **from_uid**（string）**（必填）**：要[`拖拽`](#drag)的元素的 uid
- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **to_uid**（string）**（必填）**：拖放目标元素的 uid
- **includeSnapshot**（boolean）*（可选）*：是否在响应中包含快照。默认 false。

---

### `fill`

**描述：** 在输入框、文本区域中输入文本，或从 &lt;select&gt; 元素中选择选项。

**参数：**

- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **uid**（string）**（必填）**：页面内容快照中该元素的 uid
- **value**（string）**（必填）**：要[`填充`](#fill)的值。复选框和开关用 "true" 或 "false"，单选按钮用 "true"。
- **includeSnapshot**（boolean）*（可选）*：是否在响应中包含快照。默认 false。

---

### `fill_form`

**描述：** 一次[`填充`](#fill)多个表单元素（输入框、下拉框、复选框、单选按钮）。与表单交互时，务必优先使用此工具，而不是多次单独调用 '[`fill`](#fill)' 或 '[`click`](#click)'。它明显更快、更可靠，并减少交互轮次。示例：一次调用中[`填充`](#fill)用户名、密码并勾选"记住我"。

**参数：**

- **elements**（array）**（必填）**：要[`填充`](#fill)的快照中的元素。
- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **includeSnapshot**（boolean）*（可选）*：是否在响应中包含快照。默认 false。

---

### `handle_dialog`

**描述：** 如果打开了浏览器对话框，使用此命令处理它

**参数：**

- **action**（enum: "accept", "dismiss"）**（必填）**：关闭还是接受对话框
- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **promptText**（string）*（可选）*：可选，输入到对话框中的提示文本。

---

### `hover`

**描述：** 将鼠标[`悬停`](#hover)在指定元素上

**参数：**

- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **uid**（string）**（必填）**：页面内容快照中该元素的 uid
- **includeSnapshot**（boolean）*（可选）*：是否在响应中包含快照。默认 false。

---

### `press_key`

**描述：** 按下按键或组合键。当无法使用 [`fill`](#fill)() 等其他输入方式时使用（例如键盘快捷键、导航键或特殊组合键）。注意：在 macOS 上，原生 &lt;select&gt; 元素无法通过 [`press_key`](#press_key) 用 ArrowDown/Enter 操作；请改用 [`fill`](#fill)() 操作组合框，或对选项使用 [`click`](#click)()。

**参数：**

- **key**（string）**（必填）**：按键或组合键（例如 "Enter"、"Control+A"、"Control++"、"Control+Shift+R"）。修饰键：Control、Shift、Alt、Meta
- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **includeSnapshot**（boolean）*（可选）*：是否在响应中包含快照。默认 false。

---

### `type_text`

**描述：** 使用键盘向先前聚焦的输入框输入文本

**参数：**

- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **text**（string）**（必填）**：要输入的文本
- **submitKey**（string）*（可选）*：可选，输入后按下的键。例如 "Enter"、"Tab"、"Escape"

---

### `upload_file`

**描述：** 通过指定元素上传文件。

**参数：**

- **filePaths**（array）**（必填）**：要上传的一个或多个文件路径。文件路径必须位于浏览器实例本地（而非 MCP）。
- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **uid**（string）**（必填）**：页面内容快照中文件输入元素或会打开文件选择器的元素的 uid
- **includeSnapshot**（boolean）*（可选）*：是否在响应中包含快照。默认 false。

---

### `click_at`

**描述：** 在指定坐标处点击（需要标志：--experimentalVision=true）

**参数：**

- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **x**（number）**（必填）**：x 坐标
- **y**（number）**（必填）**：y 坐标
- **dblClick**（boolean）*（可选）*：设为 true 表示双击。默认 false。
- **includeSnapshot**（boolean）*（可选）*：是否在响应中包含快照。默认 false。

---

## 导航自动化

### `close_page`

**描述：** 按索引关闭页面。最后一个打开的页面无法关闭。

**参数：**

- **pageId**（number）**（必填）**：要关闭的页面 ID。调用 [`list_pages`](#list_pages) 列出页面。

---

### `list_pages`

**描述：** 获取页面列表，包括浏览器中打开的扩展 Service Worker。

**参数：** 无

---

### `navigate_page`

**描述：** 跳转到 URL，或后退、前进、重新加载。未另行指定时使用项目 URL。

**参数：**

- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **handleBeforeUnload**（enum: "accept", "dismiss"）*（可选）*：是否自动接受或关闭此次导航触发的 beforeunload 对话框。默认 accept。
- **ignoreCache**（boolean）*（可选）*：重新加载时是否忽略缓存。
- **initScript**（string）*（可选）*：在下次导航时，每个新文档在任何其他脚本之前执行的 JavaScript 脚本。
- **timeout**（integer）*（可选）*：最大等待时间（毫秒）。设为 0 时使用默认超时。
- **type**（enum: "url", "back", "forward", "reload"）*（可选）*：按 URL 导航、在历史记录中后退或前进，或重新加载。
- **url**（string）*（可选）*：目标 URL（仅 type=url 时）

---

### `new_page`

**描述：** 打开新标签页并加载 URL。未另行指定时使用项目 URL。

**参数：**

- **url**（string）**（必填）**：在新页面中加载的 URL。
- **background**（boolean）*（可选）*：是否在后台打开页面而不置于前台。默认 false（前台）。
- **isolatedContext**（string）*（可选）*：若指定，页面将在具有给定名称的隔离浏览器上下文中创建。同一浏览器上下文中的页面共享 Cookie 和存储。不同浏览器上下文中的页面完全隔离（适用于 Cookie 和身份验证的全新测试）。
- **timeout**（integer）*（可选）*：最大等待时间（毫秒）。设为 0 时使用默认超时。

---

### `select_page`

**描述：** 选择一个页面作为后续工具调用的上下文。

**参数：**

- **pageId**（number）**（必填）**：要选择的页面 ID。调用 [`list_pages`](#list_pages) 获取可用页面。
- **bringToFront**（boolean）*（可选）*：是否聚焦页面并将其置于顶层。

---

### `wait_for`

**描述：** 等待指定文本出现在所选页面上。默认也会匹配隐藏文本。将 visible 设为 true 可等待匹配文本可见。

**参数：**

- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **text**（array）**（必填）**：非空文本列表。当任一值与可访问名称完全匹配，或是 DOM 文本的子串时完成等待。匹配区分大小写。DOM 文本保留空格和换行，而快照文本可能规范化空白。请使用原始空白或更短且有辨识度的子串。
- **timeout**（integer）*（可选）*：最大等待时间（毫秒）。设为 0 时使用默认超时。
- **visible**（boolean）*（可选）*：若为 true，等待匹配文本可见。默认隐藏文本也计入。

---

## 模拟

### `emulate`

**描述：** 在目标页面上模拟各种特性。每次调用都会替换所有模拟设置（extraHttpHeaders 除外），因此请在一次调用中传入所有所需设置。

**参数：**

- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **colorScheme**（enum: "dark", "light", "auto"）*（可选）*：[`模拟`](#emulate)深色或浅色模式。省略或设为 "auto" 可重置为默认值。
- **cpuThrottlingRate**（number）*（可选）*：表示 CPU 降速因子。省略或将速率设为 1 可禁用限速
- **extraHttpHeaders**（string）*（可选）*：额外 HTTP 头，为 JSON 字符串对象，例如 {"X-Custom": "value", "Authorization": "Bearer token"}。这些头会包含在源自该页面的每个 HTTP 请求中，并在导航间持续存在，直到被清除。传入空字符串可清除所有额外头。
- **forcedColors**（enum: "active", "none", "auto"）*（可选）*：[`模拟`](#emulate) `forced-colors` CSS 媒体特性。省略或设为 "auto" 可重置为默认值。
- **geolocation**（string）*（可选）*：要[`模拟`](#emulate)的地理位置（`&lt;latitude&gt;,&lt;longitude&gt;`）。纬度在 -90 到 90 之间。经度在 -180 到 180 之间。省略可清除地理位置覆盖。
- **networkConditions**（enum: "Offline", "Slow 3G", "Fast 3G", "Slow 4G", "Fast 4G"）*（可选）*：限制网络。省略可禁用限速。
- **prefersContrast**（enum: "more", "less", "custom", "no-preference", "auto"）*（可选）*：[`模拟`](#emulate) `prefers-contrast` CSS 媒体特性。省略或设为 "auto" 可重置为默认值。
- **prefersReducedMotion**（enum: "reduce", "no-preference", "auto"）*（可选）*：[`模拟`](#emulate) `prefers-reduced-motion` CSS 媒体特性。省略或设为 "auto" 可重置为默认值。
- **userAgent**（string）*（可选）*：要[`模拟`](#emulate)的用户代理。省略或设为空字符串可清除用户代理覆盖。
- **viewport**（string）*（可选）*：[`模拟`](#emulate)设备视口 '&lt;width&gt;x&lt;height&gt;x&lt;devicePixelRatio&gt;[,mobile][,touch][,landscape]'。'touch' 和 'mobile' 用于[`模拟`](#emulate)移动设备。'landscape' 用于[`模拟`](#emulate)横屏模式。省略可清除视口覆盖。

---

### `resize_page`

**描述：** 调整页面窗口大小，使页面具有指定尺寸

**参数：**

- **height**（integer）**（必填）**：页面高度
- **pageId**（number）**（必填）**：按 ID 定位特定页面。
- **width**（integer）**（必填）**：页面宽度

---

## 性能

### `performance_