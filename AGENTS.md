本仓库包含 Chrome DevTools 的 MCP 服务器和 CLI。

# 说明

- 仅使用 `package.json` 中的脚本运行命令。
- 使用 `npm run build` 通过 GN 和 Ninja 编译项目及测试。
- 在 `src/` 或 `tests/` 中添加、重命名或删除 `.ts` 文件时，需更新 `src/BUILD.gn` 或 `tests/BUILD.gn`。
- 使用 `npm run test` 构建并运行测试，运行全部测试以验证正确性。
- 使用 `npm run test path-to-test.ts` 构建并运行单个测试文件，例如 `npm run test tests/McpContext.test.ts`。
- 使用 `npm run format` 修复格式问题并获取 lint 错误。
- 除实验外，切勿修改 `third_party/devtools-frontend`：它是 git 子模块，是实际代码库的镜像。

## TypeScript 规则

- 不要使用 `any` 类型。
- 不要使用 `as` 关键字进行类型转换。
- 不要使用 `!` 运算符进行类型断言。
- 不要使用 `// @ts-ignore` 注释。
- 不要使用 `// @ts-nocheck` 注释。
- 不要使用 `// @ts-expect-error` 注释。
- 优先使用 `for..of` 而非 `forEach`。
- 不要对已类型安全的类型做类型检查（例如对静态类型变量做多余的 `typeof` 检查）。

## 测试规则

### 结构与关注点分离

- **优先使用基于 mock 的单元测试而非真实浏览器测试**：除非测试确实需要真实浏览器或 DevTools 协议集成（例如实时 CDP 事件、浏览器生命周期、次级会话），否则不要使用 `withMcpContext` 或启动真实浏览器。Puppeteer 已在上游测试浏览器行为；单元测试运行更快，且避免浏览器开销。
- **工具处理器测试（`tests/tools/*.test.ts`）**：
  - 测试工具处理器解析/校验参数，并以精确的预期参数调用 `page`、`context` 或 `response` 上的对应方法。
  - **不要**在 mock 中重新实现业务逻辑或状态跟踪（例如不要在 mock 方法中模拟状态变化）。
- **核心类测试（例如 `tests/McpPage.test.ts`）**：
  - 通过用 mock 依赖实例化被测的真实类来测试业务逻辑（例如用 `createMockPuppeteerPage()` 的 mock Puppeteer page 实例化 `new McpPage(...)`）。
  - 断言该类以预期参数调用底层 Puppeteer 方法。

### Mock 指南（`tests/mocks.ts`）

- **将 mock 集中到 `tests/mocks.ts`**：所有可复用的 mock 工厂都放在 `tests/mocks.ts`。直接从 `tests/mocks.ts` 导入（不要从 `tests/utils.ts` 重新导出）。
- **使用 `sinon.createStubInstance(Class)`**：不要手写 mock 对象或定义自定义 mock 接口。使用 `sinon.createStubInstance()`，所有原型方法会自动打桩。
- **类型**：mock 类型使用 `sinon.SinonStubbedInstance<Class>`（例如 `MockMcpPage`、`MockMcpContext`、`MockMcpResponse`）。
- **处理器 mock 辅助函数**：对于工具处理器，使用 `tests/mocks.ts` 中的 `const {page, context, response} = createHandlerMocks();` 一次设置全部三个 mock。
- **保持 mock 通用**：不要为特定工具或测试套件定制 mock。
- **命名约定**：辅助函数和变量名使用 `mock` 而非 `fake`（例如 `createMockPuppeteerPage`、`createMockMcpPage`）。将 mock 的 Puppeteer page 实例命名为 `pptrPage`。

### 断言与 Sinon 最佳实践

- **使用 `sinon.assert` 方法**：不要用 Node 的 `assert.ok(stub.calledOnce)` 或 `assert.deepStrictEqual(stub.firstCall.args[0], ...)` 验证 stub 调用。
- **验证精确参数**：
  - 单次调用且参数精确时，使用 `sinon.assert.calledOnceWithExactly(stub, ...args)`。
  - 后续调用使用 `sinon.assert.calledWithExactly(stub.secondCall, ...args)`。
  - 断言方法未被调用时，使用 `sinon.assert.notCalled(stub)`。
- **清理 stub**：`tests/setup.ts` 在每个测试后运行 `sinon.restore()`。不要在测试套件中添加 `afterEach(() => sinon.restore());`。
- **限定假定时器范围**：始终向 `sinon.useFakeTimers({toFake: [...]})` 传入不含 `nextTick` 的显式 `toFake` 列表。伪造 `process.nextTick` 会导致 `node:test` IPC 报告流死锁。

### 测试整洁性

- 不要为简短、自描述的 mock 函数或测试添加冗余注释或冗长 JSDoc。
- 只测试真实场景；避免测试实际使用中不可能发生的冗余或人为调用。
- 新测试文件的版权头使用当前年份（2026）。

# 提交前检查清单

在上传 PR 或推送新修订前，完成每一项。

## 范围与 PR 描述

- [ ] PR 只包含一项变更。将无关的重构、重命名、格式变更以及构建或编译器变更移到单独的 PR。
- [ ] PR 标题、提交类型（`fix:`、`feat:`、`chore:`）和压缩提交信息描述同一变更。对默认值或工具输出的变更属于 `fix:` 或 `feat:`，而非 `chore:`。
- [ ] PR 描述列出所有用户可见的行为变更，包括共享变更代码路径的其他工具的变更。
- [ ] PR 描述与已发布行为一致。删除 diff 中不包含的修复声明。
- [ ] PR 描述列出尚无生产调用者的新函数，或 PR 将其移除。
- [ ] 分支与 `main` 无合并冲突。仅在存在冲突时变基。
- [ ] 变更工具 Schema、工具描述或 CLI 选项后已运行 `npm run gen`。生成的文件已提交。
- [ ] 已运行 `npm run format`。没有行超过 80 列。
- [ ] `npm run test` 已在本地通过。所有平台上的 CI 失败由你负责。

## 设计

- [ ] 变更位于拥有数据的最低层：页面逻辑在 `McpPage`，上下文逻辑在 `McpContext`，格式化在 `src/formatters/*`。`McpResponse` 不解析页面或 worker。
- [ ] 如果 bug 属于 Puppeteer 或 devtools-frontend，先在那里修复。不要在本仓库复制或重新实现上游代码。
- [ ] Puppeteer 内部仅通过 `src/third_party/` 导入。
- [ ] 重复代码合并为一个辅助函数。同一代码块出现两次或以上时提取辅助函数。优先使用现有辅助函数或 Puppeteer 工具（例如 `Deferred`）而非新建。
- [ ] 新格式化类暴露 `toJSON()` 和 `toString()`，且 `toString()` 使用 `toJSON()` 的输出。
- [ ] 有三个或更多可选参数的函数接受一个选项对象。不要传位置 `undefined` 占位符。
- [ ] 每个默认值只在一处声明。
- [ ] 当错误分类会静默失败时，新行为默认需显式启用（例如配置选项默认需要重启，除非作者标记为可重载）。
- [ ] 无效的标志或选项组合尽可能由类型系统拒绝，而不仅靠注释。
- [ ] 不存在仅为测试服务的公共方法。共享辅助函数放在 `src/utils/*`。
- [ ] 参数校验位于 Zod Schema（`refine`、`coerce`），而非处理器中。校验在处理器改变任何状态前运行。
- [ ] 数值校验与消费方接受的范围一致。CDP 期望整数处使用 `Number.isInteger` 和范围检查。

## 正确性与边界情况

- [ ] 外部调用失败不会留下过期状态。调用被拒绝时恢复之前的值。
- [ ] 错误路径在抛出前仍生成 agent 所需的构建产物和部分结果。
- [ ] 可能被超时放弃的异步操作在恢复时检查其目标（浏览器、页面、配置）是否仍为当前。
- [ ] 锁或守卫恰好获取和释放一次。释放已释放守卫的辅助函数应抛出异常。
- [ ] 并发回调（监视器、重载、事件处理器）被串行化。瞬时失败不触发回调。
- [ ] 与其他进程竞争的文件系统操作能容忍竞争（`fs.rmSync(path, {force: true})` 或 `try/catch`），而非 `existsSync` + 无保护调用。
- [ ] 查找失败回退到之前的行为。不会将健康状态变为错误状态。
- [ ] 轮询循环或热函数中不运行同步进程启动或阻塞调用。
- [ ] `dispose()` 释放对象创建的所有资源，且对已关闭的对象也运行。
- [ ] 为某个操作添加的事件监听器在操作结束时移除。
- [ ] 页面侧缓存状态在使用前刷新。页面可能在工具调用之间重载。
- [ ] 所有作用于选中页面的工具共享的行为，对用 `defineTool` 定义的工具也运行，而不仅 `definePageTool`。
- [ ] 字符串和 URL 解析处理 userinfo、转义分隔符、端口、模式组以及空或纯空白输入。使用完整 authority，而非对首尾字符的启发式判断。
- [ ] 截断使用 `Intl.Segmenter` 字素，而非 `string.slice`。
- [ ] 密钥绝不进入日志。在每一行日志中脱敏 header、URL userinfo 和查询字符串，包括进程启动参数。
- [ ] 文件系统路径参数即使值可解析为 URL 也用 `validatePath()` 校验。错误信息说明参数期望什么。
- [ ] 跨进程边界（守护进程 IPC、CLI）的值若非原始类型，用 `JSON.stringify` 序列化。
- [ ] 仅为工具后续可解析的项发放标识符。
- [ ] 去重键对无标识字段的项绝不塌缩。使用计数器或跳过此类项。
- [ ] 错误信息指明失败的确切条件，并附相关值（pid、超时、URL、参数名）。
- [ ] 抛出的非 `Error` 值在错误信息中保留其内容。将带字符串 `message` 的对象视同 `Error`。
- [ ] 不要对非可选类型的值应用可选链和 `?? default`。
- [ ] 版本相关行为（Node、Chrome、OS）在 `engines` 字段允许的每个版本上测试，或测试不依赖它。

## 测试

- [ ] 每个新增或变更的函数都有调用它的测试。`src/` 中无调用者且无测试的函数从 PR 中移除。
- [ ] 每个"为 X 添加测试"形式的评审请求都有名称包含 X 的测试。
- [ ] bug 的新测试在无修复的 `main` 上失败。删除无论有无修复都通过的断言。
- [ ] `notCalled` 断言针对处理器在被测步骤后调用的第一个方法，而非 stub 从不运行的回调内的方法。
- [ ] 针对选中分支的测试也断言未走另一分支。
- [ ] 断言使用 `calledOnceWithExactly` 或 `calledWithExactly(stub.nthCall, ...)`。仅对无法精确的参数（例如 `AbortSignal`）或重载签名强制 `sinon.match.same` 时使用 `calledOnceWithMatch`。
- [ ] 处理器从输入派生的选项被断言，而不仅是调用次数。
- [ ] 测试名称描述所断言的行为。断言变化时重命名测试。
- [ ] 测试中不出现 `if (value) { assert... }`。在依赖断言前使用 `assert.ok(value)`。
- [ ] 真实浏览器测试仅用于 mock 无法观察的行为。每个场景每个测试文件最多一次慢速端到端运行。
- [ ] 统计请求的真实浏览器测试过滤 `favicon.ico`。
- [ ] 等待 CDP 事件的真实浏览器测试等待被测代码的会话或结果，而非新的 `createCDPSession()`。
- [ ] 测试中每个被等待的事件都有有界超时。正向路径测试轮询条件而非固定 sleep。
- [ ] 新的可复用 stub 和 fixture 位于 `tests/mocks.ts` 并基于 `sinon.createStubInstance`。没有手写 fixture 使用 `as unknown as`。
- [ ] 不传等于默认值的 mock 选项。
- [ ] 临时目录使用 `tests/utils.ts` 中的 `using dir = createTempDir(...)`，而非 `fs.mkdtemp`。
- [ ] 测试绝不读取开发者主目录，绝不调用 `process.exit`。打桩定位器并传 `exitProcess = false`。
- [ ] 真实浏览器测试替换为 mock 时，旧测试覆盖的每个分支仍有测试。
- [ ] 测试不重复另一测试已覆盖的往返。在第一个证明变更的断言后停止。

## 文档与输出

- [ ] 工具描述说明确切行为，包括参数何时被忽略或依赖另一参数。
- [ ] Skill 保持通用。在工具选择部分添加一条简短说明，而非特定功能的工作流。
- [ ] 行内注释一句话。删除重复现有 TSDoc 的注释。
- [ ] 依赖条件的响应文本仅在条件为真时打印。