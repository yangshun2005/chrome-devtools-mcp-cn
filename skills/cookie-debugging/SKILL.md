---
name: cookie-debugging
description: 使用 Chrome DevTools MCP 检查、调试和测试 Cookie、会话状态、认证问题以及 Cookie 同意合规性。适用于诊断 401/403 错误、认证重定向、会话过期、Cookie/Set-Cookie 头问题、Cookie 横幅同意合规性，或第三方 Cookie/SameSite/Partitioned Cookie 警告。
---

## 核心概念

**页面定位**：页面级工具（`list_network_requests`、`get_network_request`、`list_console_messages`、`evaluate_script`、`navigate_page`、`take_snapshot`、`click`、`lighthouse_audit`、`close_page` 等）需要 `pageId` 参数。通过 `list_pages` 或从 `new_page` 获取可用页面 ID。

### HttpOnly 与客户端存储

标记为 `HttpOnly` 的 Cookie 无法被客户端 JavaScript（`cookieStore` 或 `document.cookie`）访问或修改。但浏览器**会自动将活跃的 HttpOnly Cookie 附加到发出的 HTTP 请求头（`Cookie`）中**。

- 检查当前 `HttpOnly` 值：通过 `get_network_request` 查看任意发出 HTTP 请求的 `Cookie` 请求头。
- 检查 Cookie 的创建或配置方式：查看登录/认证响应的 `Set-Cookie` 响应头。
- 检查非 `HttpOnly` Cookie：使用 `evaluate_script` 配合现代 `cookieStore` API（`async () => await cookieStore.getAll()`）。

### 会话策略：活动标签页 vs 隔离上下文

选择合适的会话环境，避免状态污染（如残留的分析或认证令牌）：

| 策略 | 使用场景 | 设置 / 清理 |
| :--- | :--- | :--- |
| **活动标签页（当前页面）** | 诊断活跃用户会话、实时 401/403 错误或当前状态。 | 操作用户现有页面；从 `list_pages` 获取其 `pageId`。 |
| **干净状态（`isolatedContext`）** | 测试 Cookie 同意横幅、首次访问或零 Cookie 保证。 | 使用唯一 `isolatedContext`（如 `"consent-audit-1"`）调用 `new_page`。完成后调用 `close_page`。 |

### 客户端能力与限制

| 操作 | 客户端 JavaScript（`cookieStore` / `document.cookie`） | DevTools 网络与上下文工具 |
| :--- | :--- | :--- |
| **读取非 HttpOnly** | ✅ `async () => await cookieStore.getAll()` | ✅ `get_network_request`（请求 `Cookie`） |
| **读取 HttpOnly** | ❌ 被浏览器安全策略阻止 | ✅ `get_network_request`（请求 `Cookie`） |
| **检查属性**（`Domain`、`Path`、`SameSite`、`Expires`） | ✅ `async () => await cookieStore.getAll()` | ✅ `get_network_request`（响应 `Set-Cookie`） |
| **修改 / 删除非 HttpOnly** | ✅ `async () => await cookieStore.set(...)` | 不适用 |
| **修改 / 删除 HttpOnly** | ❌ JavaScript 中**静默失败** | ✅ 使用 `new_page(isolatedContext: ...)` 获得干净状态 |

> [!WARNING]
> 通过 JavaScript（`cookieStore.delete` 或 `document.cookie = "...; max-age=0"`）清除 `HttpOnly` Cookie 会静默失败。要在未认证或全新状态下测试，始终使用 `new_page` 配合 `isolatedContext` 创建新的隔离上下文。

---

## 工作流模式

### 1. 诊断认证失败与重定向（401 / 403）

当认证页面请求失败、返回 401/403 或重定向到登录页时：

1. **列出最近请求**：调用 `list_network_requests`，设置 `includePreservedRequests: true`。
2. **找到目标请求**：定位失败请求（401/403）或重定向（302/307）。
3. **检查发出的 `Cookie` 头**：使用 `reqid` 调用 `get_network_request`。
   - 确认 `Cookie` 头是否附加，以及所需令牌（如 `SESSION_ID`、`auth_token`）是否已发送。
4. **触发主动检查（若无最近请求）**：
   - 如果 Cookie 在之前的会话中设置且没有列出的网络调用，触发一个请求：
     - 使用 `navigate_page` 设置 `type: "reload"`，或
     - 调用 `evaluate_script` 执行 `() => fetch(window.location.href)`
   - 然后对新请求调用 `get_network_request` 检查活跃的 `Cookie` 头。
5. **追踪设置请求**：如果 Cookie 缺失或被拒绝：
   - 检查早期登录/握手响应中的 `Set-Cookie` 指令：
     - **Path 不匹配**：如请求发往 `/` 但 `Path=/api`。
     - **Domain 不匹配**：如 `Domain=api.example.com` 导致 `sub.example.com` 无法使用 Cookie。
     - **HTTP 上的 Secure 标志**：`Secure` Cookie 不会通过未加密的 `http://` 发送。
     - **SameSite 阻止**：`SameSite=Strict` Cookie 在跨站导航中会被省略。
     - **过期**：检查 `Expires` 或 `Max-Age` 是否已过。

### 2. Cookie 横幅与同意合规性测试

验证在同意前或拒绝时未设置任何非必要或跟踪 Cookie：

1. **从干净状态开始**：使用专用名称打开新的隔离上下文：
   ```json
   {"url": "<PAGE_URL>", "isolatedContext": "consent-test-1"}
   ```
2. **记录基线 Cookie**：在与横幅交互前，运行 `evaluate_script` 执行 `async () => await cookieStore.getAll()`。
3. **检查过早的网络请求与问题**：
   - 调用 `list_network_requests` 确保同意前未触发第三方跟踪信标。
   - 调用 `list_console_messages` 设置 `types: ["issue"]` 检查跟踪警告。
4. **与同意横幅交互**：
   - 使用 `take_snapshot` 捕获快照，定位"拒绝"或"全部拒绝"按钮的 `uid`。
   - 使用 `click` 点击该按钮。
5. **验证 Cookie 差异**：
   - 点击后运行 `evaluate_script` 执行 `async () => await cookieStore.getAll()`，确认仅存在严格必要或同意状态 Cookie。
6. **测试同意撤销（生命周期审计）**：
   - 审计同意撤回或偏好更改时：
     - 定位并点击"Cookie 设置"、"管理偏好"或页脚隐私触发器（`take_snapshot` $\rightarrow$ `click`）。
     - 取消选择非必要类别或点击"全部撤销" / "保存偏好"。
     - 重新查询 `cookieStore.getAll()` 验证之前接受的非必要 Cookie 已被清除或过期。
     - 对后续操作调用 `list_network_requests` 确保跟踪信标不再触发。
7. **清理上下文**：审计完成后调用 `close_page`，防止残留 Cookie 影响后续任务。

### 3. 审计 Cookie 安全、SameSite 与 CHIPS（Partitioned Cookie）

1. **快速通道：原生 DevTools 问题（推荐）**：
   - 调用 `list_console_messages`：
     ```json
     {
       "pageId": 1,
       "types": ["issue"],
       "includePreservedMessages": true
     }
     ```
   - 检查 `CookieIssue` 条目，例如：
     - `SameSiteNoneInsecure`：`SameSite=None` 但缺少 `Secure`。
     - `ThirdPartyCookiePhaseout`：第三方 Cookie 被阻止或限制。
     - `SchemefulSameSite`：跨 scheme Cookie 问题。
     - `PartitionedCookies`：无效的 CHIPS 分区属性。
2. **深度审计：Lighthouse 第三方 Cookie**：
   - 运行 `lighthouse_audit` 设置 `mode: "navigation"`（省略 `outputDirPath` 使用默认临时文件，或指定工作区内的目录）。
   - **从返回的 `report.json` 路径中提取特定 Cookie 审计**，无需将完整报告加载到上下文：
     ```bash
     node -e "const r=require('<path/to/report.json>'); const a=r.audits['third-party-cookies']; console.log(JSON.stringify({score: a?.score, displayValue: a?.displayValue, items: a?.details?.items}))"
     ```

### 4. 客户端 Cookie 检查与操作

针对客户端可访问的非 `HttpOnly` Cookie（如 UI 偏好、非敏感功能标志）：

1. **读取 Cookie 与属性**：
   - 使用现代异步 Cookie Store API：
     ```js
     async () => await cookieStore.getAll();
     ```
   - _非安全 HTTP 源的备选方案_：`() => document.cookie`。
2. **设置 / 修改 Cookie**：
   - 通过 `cookieStore` 设置客户端 Cookie：
     ```js
     async () =>
       await cookieStore.set({
         name: 'theme',
         value: 'dark',
         expires: Date.now() + 86400000,
         sameSite: 'lax',
       });
     ```
3. **删除 Cookie**：
   - 清除客户端 Cookie：
     ```js
     async () => await cookieStore.delete('theme');
     ```

---

## 故障排查

- **`cookieStore` 未定义**：`cookieStore` 需要安全上下文（`https://`、`localhost` 或 `127.0.0.1`）。在非安全 HTTP 源上，使用 `() => document.cookie` 或通过 HTTPS 测试。
- **`evaluate_script` 返回空 / 未解析的 Promise**：`cookieStore` 方法是异步的。始终用 `async () => await cookieStore.getAll()` 包裹调用。
- **JavaScript 中看不到 Cookie**：该 Cookie 标记为 `HttpOnly`。触发网络请求并调用 `get_network_request` 在 `Cookie` 请求头中查看。
- **JavaScript 删除未移除 Cookie**：该 Cookie 是 `HttpOnly`，或需要匹配的 `Path` 和 `Domain` 参数。使用 `new_page` 配合新的 `isolatedContext` 获得干净状态。
- **Cookie 在响应中设置但请求中未发送**：
  - 检查页面是否为 `http://` 而 Cookie 指定了 `Secure`。
  - 检查 `Domain` 是否限制了子域名。
  - 检查 `list_console_messages(types: ["issue"])` 中的浏览器拒绝原因。
- **残留 Cookie 污染审计**：运行合规测试时始终使用 `new_page` 配合唯一 `isolatedContext`，完成后调用 `close_page`。