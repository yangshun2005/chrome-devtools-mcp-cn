# 实验性：在 Android 上调试 Chrome

这是实验性功能，Puppeteer 并未官方支持将 Android 上的 Chrome 作为目标。

以下流程适用于大多数用户。如需更多帮助，请参阅[故障排查：DevTools 未检测到 Android 设备](https://developer.chrome.com/docs/devtools/remote-debugging#troubleshooting)。

1. 在 Android 上打开开发者选项界面。请参阅[配置设备上的开发者选项](https://developer.android.com/studio/debug/dev-options.html)。
2. 选择启用 USB 调试。
3. 使用 USB 数据线将 Android 设备直接连接到开发机。
4. 在开发机上设置从开发机到 Android 设备的端口转发：
   ```shell
   adb forward tcp:9222 localabstract:chrome_devtools_remote
   ```
5. 配置 MCP 服务器以连接 Chrome
   ```json
   "chrome-devtools": {
     "command": "npx",
     "args": [
       "chrome-devtools-mcp@latest",
       "--wsEndpoint=ws://127.0.0.1:9222/devtools/browser/"
     ],
     "trust": true
   }
   ```
6. 在编码代理中运行以下提示词，测试配置是否正常：
   ```none
   Check the performance of developers.chrome.com
   ```

现在，Chrome DevTools MCP 服务器应能控制 Android 设备上的 Chrome。