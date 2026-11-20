# 更新日志

## [1.13.0](https://github.com/ChromeDevTools/chrome-devtools-mcp/compare/chrome-devtools-mcp-v1.12.0...chrome-devtools-mcp-v1.13.0) (2026-11-11)


### 🎉 新特性

* 基于 DevTools BreakpointManager 添加 logpoints ([#3211](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3211)) ([0877ee0](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/0877ee00d2e43c1c04bba275e7d08cc1033e36e9))
* 添加页面级网络 mock ([#3384](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3384)) ([1d692d3](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/1d692d31ae370105afcf4d826dc36f1c4c22e21a))
* 支持上游网络 mock 响应 ([#3420](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3420)) ([c4bf342](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/c4bf342b0854620d2e41262a971e1b24b705f957))


### 🛠️ 修复

* **config:** 接受带 UTF-8 BOM 的配置文件 ([#3365](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3365)) ([63170b1](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/63170b11a2fa74d5ceea757194d7b25f3e145f68))
* **console:** 循环的 Error.cause 链在 10 个 cause 后停止解析 ([#3338](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3338)) ([172656a](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/172656ab38a4ff666f88dc9032fa841834a69495))
* 设置 --no-javascript-evaluation 时禁用 set_logpoints ([#3415](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3415)) ([11fcc59](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/11fcc596290eb73658a3afad89afc037595172e5)), closes [#3414](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3414)
* **extensions:** 说明 list_extensions 仅列出未打包的扩展 ([#3433](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3433)) ([1383bb9](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/1383bb9b4145227976e49a5dd010aa105b638e44))
* 更新检查中忽略无效的缓存版本 ([#3392](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3392)) ([3a9f378](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/3a9f3780d23a765ed0125d139338d41d311701c0))
* **input:** 在 Zod schema 中校验 press_key 和 submitKey 参数 ([#3382](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3382)) ([375a269](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/375a269ee0905a8a30bd15827e701bc1a94d9d9e))
* **network:** 无请求的导航使用空请求列表 ([#3357](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3357)) ([b701776](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/b7017767a4b230509670a27ffee20a8255e3554f))
* 规范化工具处理器抛出的非 Error 值 ([#3068](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3068)) ([535a3dc](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/535a3dc619498240e25a4ab1b9d9ed37a68579ad))
* **performance:** 在未录制 trace 的页面上调用 performance_stop_trace 时报错 ([#2971](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/2971)) ([#3117](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3117)) ([1ab630a](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/1ab630aecc558ba1626851982cdec13f7c4659f6))
* **plugin:** 使用 npx --prefix 启动 Claude Code MCP 服务器 ([#3353](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3353)) ([a24334d](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/a24334d3a1733996438c3c4786ab4f880aaaf044))
* 请求取消时保留任务序列化 ([#3079](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3079)) ([03fd4e8](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/03fd4e840c6afc8b86f1d2dd9d94b41dcff8a326))
* 报告保持 URL 不变的页面加载 ([#3395](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3395)) ([3cc38bb](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/3cc38bb3640a7f3c4a24f10258b44f5a5ae808a3))
* 逐字节保存二进制请求体 ([#3404](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3404)) ([e3eb901](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/e3eb901fcc1de3a3cbf1be39d22e0d306a0ae23d))
* Chrome 找不到 X server 时建议使用 --headless ([#3428](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3428)) ([8b0856a](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/8b0856ad3f403fb560159eec563365632f5d0e58))
* **url:** validateUrl 中所有 scheme 检查前先解包 view-source: ([#3335](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3335)) ([7275268](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/7275268bf05258d8cff8d18ace79848adbf27cac))
* **wait:** 说明对空白字符敏感的文本匹配 ([#3350](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3350)) ([81a0379](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/81a037939b19a3588ac6a35e06d576af706a3b57))


### 📄 文档

* 添加 Pi 客户端配置 ([#3364](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3364)) ([bc0d93e](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/bc0d93ee2df6f2410d9e9704903c3547acae7c9a))
* 澄清自定义浏览器启动和连接选项 ([#3386](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3386)) ([7e10c08](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/7e10c08d30172f4a9d0dba64fa21890afebed09a))
* **input:** 说明 press_key 在 macOS 上无法驱动原生 &lt;select&gt; 弹窗 ([#3288](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3288)) ([8e79bd0](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/8e79bd09632613d24c3124c367e874dc85469602))


### ⚡ 性能

* **memory:** NetworkCollector 中丢弃请求发起者的异步父级 ([#3345](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3345)) ([ca6f834](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/ca6f83498014e74243117a2aa3830cf71091d64d)), closes [#3344](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3344)


### 🏗️ 重构

* 从 third_party barrel 仅重新导出用到的 Zod 运行时成员 ([#3439](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3439)) ([6b056c8](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/6b056c848a981c5ecb493f6e22edd6701964a06d))
* 复用 Zod schema 并校验整数视口尺寸 ([#3367](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3367)) ([3c609da](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/3c609da38a39067f4a0b885ce81968eb958b76a8))

## [1.12.0](https://github.com/ChromeDevTools/chrome-devtools-mcp/compare/chrome-devtools-mcp-v1.11.0...chrome-devtools-mcp-v1.12.0) (2026-10-22)


### 🎉 新特性

* 添加 --maxNavigationSaved 标志以配置保留的导航历史 ([#3018](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3018)) ([a580d13](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/a580d1320ea47cb740c722e6abbccdc2b074d402))
* 添加 Kimi Code 插件清单 ([#3029](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3029)) ([cd00b64](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/cd00b64f9d628c9bf3bb9fb81ea4e4c9a64a5886))
* 无需浏览器连接即可分析堆快照 ([#3156](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3156)) ([78c8320](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/78c8320c505a7725980a10cb420b72fa59a7322e))
* **config:** 添加 --watchConfig 以重新加载配置文件变更 ([#3248](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3248)) ([b48561f](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/b48561f6ca3b5d34a19ba39bd820de369690a1cb))
* **config:** 引入分层 ServerConfig 以支持热重载 ([#3203](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3203)) ([37f1fda](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/37f1fda81b91df7b9ca851e3f5af8d08f50b4846))
* **config:** 与 McpContext 和 McpPage 共享 ReadonlyServerConfig ([#3204](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3204)) ([0813bf3](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/0813bf3c51cace8e04f521f40f9b5eac57fba4fd))
* **config:** 支持重新加载 blockedUrlPattern 和 allowedUrlPattern ([#3268](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3268)) ([cc850f5](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/cc850f5ad9160a07926dfa2e8892edde47667e07))
* **context:** 配置重载时释放已禁用特性的状态 ([#3049](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3049)) ([c0140d1](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/c0140d1b4a16cfb92bf16a5beb6f263a379df834))
* 堆快照工具默认分页 ([#3201](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3201)) ([fb8375b](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/fb8375bd2d0c5d47bd46e2276b05054112c12888))
* compare_heapsnapshots 中详细差异分页 ([#3195](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3195)) ([4a9dd53](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/4a9dd53fe639d5bbd9612965c3da2d7a5df3dd4a))
* 使用 TaskRunner 并行运行独立工具调用 ([#3243](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3243)) ([8a8d877](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/8a8d8771c2417335e5afd241007784ab7f534080))
* 在不同页面上并行运行页面级工具 ([#3252](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3252)) ([f8f18ac](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/f8f18ac25350bd6e8d771e63227858c3c5b2f357))
* **server:** 添加 McpServer.applyConfig() 以重新加载配置 ([#3051](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3051)) ([f874aeb](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/f874aeb96ae58587c509d3da4707bcba1a4074b5))
* **slim:** slim 模式截图支持 filepath ([#3296](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3296)) ([d399c4a](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/d399c4a41ea46c023815ce88c4962cb61bfea81a))
* compare_heapsnapshots 中按自身大小对类对象排序 ([#3205](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3205)) ([f8a18c6](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/f8a18c68d4b65f6af01a6d4ba190f61f4b8a755e))
* **tools:** 支持就地更新 ToolHandler 定义 ([#3050](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3050)) ([8064138](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/8064138482a2ab75a3aef11b000c4786ec98aa9c))
* **wait_for:** 支持可选的可见文本匹配 ([#3212](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3212)) ([eef92da](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/eef92da078c30fd6d91b57f1dfbde5ab9ea92da2))


### 🛠️ 修复

* 允许文件系统根工作区根目录下的路径 ([#3277](https://github.com/ChromeDevTools/chrome-devtools-mcp/issues/3277)) ([296ccef](https://github.com/ChromeDevTools/chrome-devtools-mcp/commit/296ccef3dd44d063c991ce59c96ddf7a43cbcb8d))
* 对话框打开时阻止 evaluate_