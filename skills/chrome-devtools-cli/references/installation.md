# 安装

全局安装该包，使 `chrome-devtools` 命令可用。只需在首次使用时执行一次。

```sh
npm i chrome-devtools-mcp@latest -g
chrome-devtools status # check if install worked.
```

## 故障排查

- **找不到命令：** 如果 `chrome-devtools` 无法识别，请确认全局 npm `bin` 目录已加入系统 `PATH`。重启终端或重新加载 shell 配置文件（如 `.bashrc`、`.zshrc`）。
- **权限错误：** 如果安装时遇到 `EACCES` 或权限错误，不要使用 `sudo`。改用 `nvm` 等 Node 版本管理器，或配置 npm 使用其他全局目录。
- **运行的是旧版本：** 重新安装前先执行 `chrome-devtools stop && npm uninstall -g chrome-devtools-mcp`，或确认 `PATH` 中指向的是最新版本。