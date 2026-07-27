<!-- markdownlint-disable MD013 -->

# 复制给智能体安装 / Copy this to an agent

把下面这段话复制给 Codex、Claude Code、Cursor Agent、ChatGPT Agent 或其他本地智能体。执行流程只有一次确认：确认是否写入持久化入口文件。

```text
请使用 https://github.com/Jia-Ethan/zcode-keysmith 帮我安装 ZCode App 的 managed true system-role entrypoint。

执行要求：
1. 先阅读 README.md 和 zcode-keysmith.py。
2. 运行：python3 zcode-keysmith.py install --dry-run。
3. 向我展示将写入的准确路径，必须包括：
   - ~/.zcode-keysmith/system-role.md
   - ~/.zcode-keysmith/config.json
   - ~/.zcode-keysmith/bin/zcode-agent-wrapper.py
   - ~/.zcode-keysmith/bin/zcode-keysmith-env.sh
   - ~/Library/LaunchAgents/com.jia.zcode-keysmith.env.plist
4. 同时展示将使用的 ZCode runtime 路径、ZCode node command 路径、agent-server args，以及 app_bundle_modified: false。
5. API key、token、MCP 配置、ZCode provider 配置由 ZCode 自身管理；安装器不读取、不保存、不打印这些内容。
6. 只问我一次：是否确认写入以上持久化入口文件。
7. 我确认后，运行：python3 zcode-keysmith.py install --yes。
8. 写入后运行：python3 zcode-keysmith.py doctor。
9. 再运行：python3 zcode-keysmith.py verify。
10. 提醒我重新打开 ZCode，然后新建任务测试"你是谁"。测试后再次运行 verify，确认 wrapper_invoked: true。
```

English version:

```text
Use https://github.com/Jia-Ethan/zcode-keysmith to install the managed true system-role entrypoint for my local ZCode App.

Requirements:
1. Read README.md and zcode-keysmith.py first.
2. Run: python3 zcode-keysmith.py install --dry-run.
3. Show the exact write targets:
   - ~/.zcode-keysmith/system-role.md
   - ~/.zcode-keysmith/config.json
   - ~/.zcode-keysmith/bin/zcode-agent-wrapper.py
   - ~/.zcode-keysmith/bin/zcode-keysmith-env.sh
   - ~/Library/LaunchAgents/com.jia.zcode-keysmith.env.plist
4. Also show the ZCode runtime path, ZCode node command path, agent-server args, and app_bundle_modified: false.
5. API keys, tokens, MCP config, and provider config stay managed by ZCode. The installer must not read, store, or print them.
6. Ask once whether to write the managed entrypoint files.
7. After confirmation, run: python3 zcode-keysmith.py install --yes.
8. Then run: python3 zcode-keysmith.py doctor.
9. Then run: python3 zcode-keysmith.py verify.
10. Tell me to reopen ZCode and test a fresh task with "Who are you?". After the test, run verify again and confirm wrapper_invoked: true.
```

## 友链 / Community

本项目接受 LINUX DO 社区佬友监督与反馈：[LINUX DO](https://linux.do)

同系列项目 / Same series:

- [codex-keysmith](https://github.com/Jia-Ethan/codex-keysmith) - Codex CLI 本地配置的版本化指令部署工具，支持预览、hook 隔离、中断恢复与分层卸载。
- [claude-keysmith](https://github.com/Jia-Ethan/claude-keysmith) - Claude Code `CLAUDE.md` 的受管理 import-block 安装器，用于本地 Markdown 指令文件。
- [grok-keysmith](https://github.com/Jia-Ethan/grok-keysmith) - Grok Build 的全局 `AGENTS.md` 指令部署工具，支持 compat/hook 隔离、中断恢复与分层卸载。
- [zcode-keysmith](https://github.com/Jia-Ethan/zcode-keysmith) - ZCode App 的受管理 true system-role 入口，通过 agent-server wrapper 将 `system-role.md` 接入 runtime `customSystemPrompt` 的 system-message 路径。
