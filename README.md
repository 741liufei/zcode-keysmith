# zcode-keysmith

<p align="center">
  <strong>ZCode App managed true system-role entrypoint.</strong>
</p>

<p align="center">
  <a href="#简体中文">简体中文</a> ·
  <a href="README.en.md">English</a> ·
  <a href="docs/reference.md">Reference</a> ·
  <a href="docs/agent-install.md">智能体安装 / Agent install</a> ·
  <a href="LICENSE">License</a>
</p>

<p align="center">
  <img alt="ZCode App" src="https://img.shields.io/badge/ZCode-App-111111">
  <img alt="system role" src="https://img.shields.io/badge/true-system--role-555555">
  <img alt="Python" src="https://img.shields.io/badge/Python-3.10%2B-3776AB">
  <img alt="License" src="https://img.shields.io/badge/license-MIT-6DB33F">
</p>

## 简体中文

### 这是什么

`zcode-keysmith` 为本机 ZCode 桌面端安装一条受管理的 `system-role.md` 入口。它把仓库里的 `examples/system-role.md` 安装成用户目录里的受管理版本，让 ZCode 新启动的 agent-server 进程读取这份文件，进入 ZCode runtime 的 system message 注入路径。**ZCode App 原包保持完整**：安装器只写用户目录和用户 LaunchAgent；API key、provider、MCP、settings 和项目文件继续由 ZCode 自身管理，安装器不读取、不保存、不打印这些内容。

默认写入位置：

```text
~/.zcode-keysmith/system-role.md
~/.zcode-keysmith/config.json
~/.zcode-keysmith/bin/zcode-agent-wrapper.py
~/.zcode-keysmith/bin/zcode-keysmith-env.sh
~/Library/LaunchAgents/com.jia.zcode-keysmith.env.plist
```

### 原理

ZCode 桌面端启动 agent-server 时会读取两个环境变量：

```text
ZCODE_AGENT_SERVER_COMMAND
ZCODE_AGENT_SERVER_ARGS_JSON
```

`zcode-keysmith` 把 `ZCODE_AGENT_SERVER_COMMAND` 指向 `~/.zcode-keysmith/bin/zcode-agent-wrapper.py`。wrapper 做三件事：

1. 读取 ZCode 自带 runtime：`/Applications/ZCode.app/Contents/Resources/glm/zcode.cjs`；
2. 在用户目录缓存一份 runtime 副本，只替换一处 `customSystemPrompt` 入口，让它优先读取 `~/.zcode-keysmith/system-role.md`；
3. 用 ZCode 自带的 Electron node 命令启动缓存 runtime。优先使用 Helper 可执行文件（`ZCode Helper.app`），避免 macOS Dock 把后端 agent-server 识别成另一个前台 ZCode；当前 ZCode 包没有 Helper 可执行文件时才回退到主可执行文件。

ZCode runtime 内部会把 `customSystemPrompt` 放进 `injectionTarget: "system"` 的上下文段。这样，`system-role.md` 进入的是 ZCode runtime 的 system message 路径，而不是普通项目说明文件。安装时会对源提示词做一次格式归一化：如果 `examples/system-role.md` 来自 GLM ChatML 导出，外层 `<|im_start|>system:` / `<|im_end|>` 传输标记会被清理，写入 ZCode 的是 system prompt 主体。

### 安装

```bash
python3 zcode-keysmith.py install --dry-run   # 先看写入计划
python3 zcode-keysmith.py install --yes        # 确认后写入
python3 zcode-keysmith.py doctor               # 检查状态
python3 zcode-keysmith.py verify                # 检查链路
```

安装器会备份已有受管理文件为 `<filename>.bak_YYYYMMDD_HHMMSS`。`--dry-run` 优先级最高，即使同时传 `install --yes --dry-run`，也只预览。

安装器会更新当前 macOS launchd 环境，并写入用户 LaunchAgent，用于后续登录会话恢复同一组环境变量。**已经打开的 ZCode 主进程需要重新打开一次**才能继承新的 agent-server entrypoint。完成安装后按这个顺序验证：

1. 退出 ZCode，重新打开；
2. 新建任务，输入「你是谁」；
3. 回到终端运行 `python3 zcode-keysmith.py verify`；`wrapper_invoked: true` 表示 ZCode 已经实际启动过受管理 wrapper。

完整字段含义和 `doctor`/`verify` 输出见 [`docs/reference.md`](docs/reference.md)。

### 卸载

```bash
python3 zcode-keysmith.py uninstall --dry-run   # 预览
python3 zcode-keysmith.py uninstall --yes        # 确认移除
```

卸载会把受管理文件改名为 `.bak_YYYYMMDD_HHMMSS`，并清理当前 launchd 环境里的 keysmith entrypoint。ZCode App 原包保持完整。

### ZCode 不在默认路径

```bash
python3 zcode-keysmith.py install --zcode-app /path/to/ZCode.app --dry-run
# 或
ZCODE_APP_PATH=/path/to/ZCode.app python3 zcode-keysmith.py install --dry-run
```

### 项目结构与验证

项目结构、`py_compile`/`pytest` 验证步骤见 [`docs/reference.md`](docs/reference.md)。

---

English version: [`README.en.md`](README.en.md). 智能体安装提示词和同系列项目见 [`docs/agent-install.md`](docs/agent-install.md)。
