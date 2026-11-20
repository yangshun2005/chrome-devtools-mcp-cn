# 开发者指南：构建第三方开发者工具

本文档介绍如何将 Web 应用中的自定义运行时数据和工具暴露给 Chrome DevTools for Agents。

## 概述

第三方开发者工具让 Web 应用能够暴露内部状态、组件层级或无法通过静态分析推断的特定调试数据。这样，Chrome DevTools for Agents 就能在调试会话中为 AI agent 提供更丰富、更可操作的上下文。

## 工作原理：工具发现

Chrome DevTools for Agents 通过基于事件的机制发现页面暴露的工具。流程如下：

1.  **事件派发：** Chrome DevTools for Agents 在全局 `window` 对象上派发 `devtoolstooldiscovery` 事件。
2.  **监听：** 应用监听该事件并提供工具定义。
3.  **响应：** 应用必须调用 `event.respondWith()` 注册 `ToolGroup` 对象。

_注意：Chrome DevTools for Agents 会在页面导航（如 `new_page`、`navigate_page`）后自动请求该列表，或通过 `list_3p_developer_tools()` MCP 工具显式请求。_

## 实现

要暴露工具，请为 `devtoolstooldiscovery` 事件实现监听器，并提供包含工具定义的 `ToolGroup`。

### 类型定义

工具必须遵循 `ToolDefinition` 和 `ToolGroup` 接口：

```typescript
export interface ToolDefinition {
  name: string;
  description: string;
  inputSchema: JSONSchema7;
  execute: (args: Record<string, unknown>) => unknown;
}

export interface ToolGroup {
  name: string;
  description: string;
  tools: ToolDefinition[];
}
```

### 示例实现

```typescript
window.addEventListener(
  'devtoolstooldiscovery',
  (event: DevtoolsToolDiscoveryEvent) => {
    event.respondWith({
      name: 'Page-specific DevTools',
      description: "Provide runtime info directly from the page's JavaScript",
      tools: [
        {
          name: 'add',
          description: 'Calculates the sum of two numbers.',
          inputSchema: {
            type: 'object',
            properties: {
              a: {type: 'number'},
              b: {type: 'number'},
            },
            required: ['a', 'b'],
          },
          execute: async (input: {a: number; b: number}) => {
            return input.a + input.b;
          },
        },
      ],
    });
  },
);
```

## 工具调用

工具被发现后，MCP 客户端可通过 Chrome DevTools for Agents 执行：

- **`execute_3p_developer_tool`**：按名称调用已注册工具的标准方式，参数会经过校验。
- **`evaluate_script`**：运行自定义脚本直接调用 `window.__dtmcp.executeTool()`，实现更复杂的交互和功能组合。

## 重要注意事项

- **实验性状态：** 此功能目前为实验性。API 可能变更，不保证稳定性。
- **安全与范围：**
  - **上下文：** 第三方开发者工具仅在定义它们的页面上下文中执行，不跨源持久化。
  - **能力：** 这些工具不会授予额外权限；只能执行攻击者本就能在该页面运行的代码。
- **DOM 元素：** 如果工具需要 DOM 元素作为输入或输出，将通过无障碍树中引用的特殊 UID 处理。
- **标志：** 该实现由命令行标志 `--categoryExperimentalThirdParty=true` 控制。