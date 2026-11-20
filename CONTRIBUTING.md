# 如何贡献

我们欢迎你向本项目提交补丁和贡献。

## 开始之前

### 签署贡献者许可协议

向本项目提交贡献必须附带
[贡献者许可协议](https://cla.developers.google.com/about)（CLA）。
你（或你的雇主）保留贡献的版权；该协议仅授予我们将你的贡献作为项目一部分使用和再分发的权限。

如果你或你当前的雇主已经签署过 Google CLA（即使是为其他项目签署的），通常无需再次签署。

访问 <https://cla.developers.google.com/> 查看你当前的协议或签署新协议。

### 阅读社区准则

本项目遵循
[Google 开源社区准则](https://opensource.google/conduct/)。

## 开发流程

### 代码审查

所有提交（包括项目成员的提交）都需要审查。我们使用 GitHub Pull Request 进行审查。有关使用 Pull Request 的更多信息，请参阅
[GitHub 帮助](https://help.github.com/articles/about-pull-requests/)。

### 约定式提交

PR 和提交标题请遵循[约定式提交](https://www.conventionalcommits.org/en/v1.0.0/)。

### 功能发布检查清单

对于包含尚未对用户开放的不完整功能的提交，请使用 `chore:`。功能准备发布后，创建一个带 `feat:` 前缀的 PR 来启用该功能。需满足以下条件：

- 功能文档已更新。例如，README.md 和工具参考已更新。
- 功能可在 Chrome 稳定版中使用，否则需记录版本限制。
- 相应技能已更新，或按需添加新技能。
- 功能可独立或与现有功能配合满足用例（我们希望避免提供了一些工具但无法成功调试的功能）。

### 发布流程

`chrome-devtools-mcp` 的发布由 GitHub Actions 自动化。要发布新版本，请[搜索标题为 `chore(main): release chrome-devtools-mcp` 的 PR](https://github.com/ChromeDevTools/chrome-devtools-mcp/pulls?q=is%3Apr+is%3Aopen+%22chore%28main%29%3A+release+chrome-devtools-mcp%22)，审查、测试并合并。如果主分支上有变更出现在变更日志中，发布 PR 会自动创建。

### 如何更新 Lighthouse 依赖

- 更新 package.json 中的 Lighthouse 版本并运行 `npm install`。npm 版本当前用于类型。
- 将对应的 Lighthouse 仓库修订版检出到同级目录（`../lighthouse`）。
- 运行 `npm run update-lighthouse`（注意 Lighthouse 需要 yarn）。
- 提交打包产物。如果通过打包新增了依赖，请更新 `tests/third_party_notices.test.ts`。

## 安装

确认你使用的 node 版本符合 .nvmrc 中的指定版本，然后运行以下命令：

```sh
git clone https://github.com/ChromeDevTools/chrome-devtools-mcp.git
cd chrome-devtools-mcp
npm run sync
npm run build
```

### 使用 @modelcontextprotocol/inspector 测试

```sh
npx @modelcontextprotocol/inspector node ./out/Default/gen/src/bin/chrome-devtools-mcp.js
```

### 使用 MCP 客户端测试

将 MCP 服务器添加到客户端配置中。

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "node",
      "args": [
        "/<path-to-chrome-devtools-mcp>/out/Default/gen/src/bin/chrome-devtools-mcp.js"
      ]
    }
  }
}
```

#### 配合 VS Code SSH 使用

运行 `@modelcontextprotocol/inspector` 时会启动 2 个服务——一个在端口 `6274`，一个在 `6277`。
通常 VS Code 会自动检测并转发 `6274`，但无法检测 `6277`，因此需要手动转发。

### 调试

要将调试日志写入工作目录下的 `log.txt`，请运行以下命令：

```sh
npx @modelcontextprotocol/inspector node ./out/Default/gen/src/bin/chrome-devtools-mcp.js --log-file=/your/desired/path/log.txt
```

你可以像往常一样使用 `NODE_DEBUG` 环境变量控制记录的类别。

### 更新文档

添加新工具或更新工具名称/描述时，请务必运行 `npm run gen` 生成工具参考文档。

### 为 Evals 贡献

我们使用 Gemini 评估 `scripts/eval_scenarios` 中的 MCP 服务器工具。
每个场景是一个 TypeScript 文件，导出一个实现 `TestScenario` 的 `scenario` 对象。

- **prompt**：发送给模型的提示。
- **maxTurns**：最大对话轮数。
- **expectations**：验证模型所做工具调用的函数。
- **htmlRoute**（可选）：在特定路径为测试提供自定义 HTML 内容。

我们旨在测试工具是否被正确使用，而不做过于严格的断言。避免断言可能变化的精确参数值（例如自然语言推理），但要确保核心参数（如 URL 或选择器）正确。

示例：

```ts
import {TestScenario} from '../eval_gemini.js';

export const scenario: TestScenario = {
  prompt: 'Navigate to example.com',
  maxTurns: 2,
  expectations: calls => {
    // Check that at least one call was 'browse_page'
    const navigation = calls.find(c => c.name === 'browse_page');
    if (!navigation) throw new Error('Model did not browse the page');
    // Verify essential args
    if (navigation.args.url !== 'http://example.com') {
      throw new Error(`Wrong URL: ${navigation.args.url}`);
    }
  },
};
```

## JSON Schema 限制

- 不使用 .nullable()，不使用 .object() 类型。由 `@local/enforce-zod-schema` ESLint 规则强制执行。
- 将复杂对象表示为简短的格式化字符串。