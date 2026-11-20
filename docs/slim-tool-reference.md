<!-- AUTO GENERATED DO NOT EDIT - run 'npm run gen' to update-->

# Chrome DevTools MCP 精简工具参考

- **[导航自动化](#navigation-automation)**（1 个工具）
  - [`navigate`](#navigate)
- **[调试](#debugging)**（2 个工具）
  - [`evaluate`](#evaluate)
  - [`screenshot`](#screenshot)

## 导航自动化

### `navigate`

**描述：** 加载 URL

**参数：**

- **url**（string）**（必填）**：要 [`navigate`](#navigate) 到的 URL

---

## 调试

### `evaluate`

**描述：** 执行 JavaScript 脚本

**参数：**

- **script**（string）**（必填）**：要在页面上运行的 JS 脚本

---

### `screenshot`

**描述：** 截取 png [`screenshot`](#screenshot)

**参数：**

- **filePath**（string）_（可选）_：保存 [`screenshot`](#screenshot) 的绝对路径，或相对于当前工作目录的路径；指定后将保存到文件，而非附加到响应中。

---