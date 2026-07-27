<!-- markdownlint-disable MD013 -->

# 命令参考与可观测性字段 / Command reference and observability fields

日常使用只需要 [`README.md`](../README.md) 的「安装」和「原理」；本页是完整字段表和验证步骤。

---

## 简体中文

### 可观测性

`zcode-keysmith` 会记录 wrapper 启动日志：

```text
~/.zcode-keysmith/logs/wrapper-start.jsonl
```

每次 ZCode 通过 wrapper 启动 agent-server 时，日志会追加一行 JSON，包含启动时间、PID、agent-server 参数、缓存 runtime 路径和 system prompt 路径。日志不包含 API key、token、cookie 或 MCP secret。

本地链路检查：

```bash
python3 zcode-keysmith.py verify
```

重点字段：

| 字段 | 含义 |
|---|---|
| `wrapper_smoke` | wrapper 能否本地启动并进入 ZCode CLI help，不发送模型请求 |
| `wrapper_invoked` | 是否存在 wrapper 启动日志 |
| `last_wrapper_start` | 最近一次 wrapper 启动时间 |
| `zcode_agent_override_supported` | ZCode App 是否包含 agent-server 环境入口 |
| `zcode_runtime_patchable` | 当前 runtime 是否匹配 system prompt 入口形态 |
| `zcode_running` | 当前 ZCode 主进程是否正在运行 |

如果 `wrapper_smoke: true` 但 `wrapper_invoked: false`，通常表示受管理入口已经准备好，但 ZCode 还没有重新打开，或还没有新建会触发 agent-server 的任务。

### 状态检查

```bash
python3 zcode-keysmith.py doctor
```

`doctor` 会显示：

- 受管理目录是否存在；
- `system-role.md` 是否存在；
- wrapper 是否存在；
- LaunchAgent 是否存在；
- ZCode runtime 是否存在并匹配当前入口形态；
- launchd 环境变量是否指向受管理入口；
- API key 状态：固定显示为 `not read or stored`。

如果 ZCode 不在 `/Applications/ZCode.app`，可以指定 App 路径：

```bash
python3 zcode-keysmith.py install --zcode-app /path/to/ZCode.app --dry-run
python3 zcode-keysmith.py install --zcode-app /path/to/ZCode.app --yes
python3 zcode-keysmith.py verify --zcode-app /path/to/ZCode.app
```

也可以用环境变量：

```bash
ZCODE_APP_PATH=/path/to/ZCode.app python3 zcode-keysmith.py install --dry-run
```

### 项目结构

```text
zcode-keysmith/
├── zcode-keysmith.py
├── examples/
│   └── system-role.md
├── tests/
│   └── test_zcode_keysmith.py
├── docs/
│   ├── reference.md
│   ├── agent-install.md
│   └── legacy/
├── .gitignore
├── README.md / README.en.md
└── LICENSE
```

### 验证

```bash
python3 -m py_compile zcode-keysmith.py
python3 -m pytest tests -q
python3 zcode-keysmith.py install --dry-run
python3 zcode-keysmith.py doctor
python3 zcode-keysmith.py verify
```

---

## English

### Observability

`zcode-keysmith` records wrapper start events:

```text
~/.zcode-keysmith/logs/wrapper-start.jsonl
```

Every time ZCode launches agent-server through the wrapper, one JSON line is appended with the start time, PID, agent-server args, cached runtime path, and system prompt path. The log never contains an API key, token, cookie, or MCP secret.

```bash
python3 zcode-keysmith.py verify
```

Key fields:

| Field | Meaning |
|---|---|
| `wrapper_smoke` | Whether the wrapper can launch locally and reach ZCode CLI help, without sending a model request |
| `wrapper_invoked` | Whether a wrapper start log entry exists |
| `last_wrapper_start` | Timestamp of the most recent wrapper start |
| `zcode_agent_override_supported` | Whether the ZCode app bundle exposes the agent-server environment entrypoint |
| `zcode_runtime_patchable` | Whether the current runtime matches the expected system-prompt entrypoint shape |
| `zcode_running` | Whether the ZCode main process is currently running |

If `wrapper_smoke: true` but `wrapper_invoked: false`, the managed entrypoint is usually ready but ZCode has not been reopened yet, or no task has triggered agent-server since.

### Status check

```bash
python3 zcode-keysmith.py doctor
```

Shows whether the managed directory, `system-role.md`, wrapper, and LaunchAgent exist; whether the ZCode runtime matches the expected entrypoint shape; whether launchd environment variables point at the managed entrypoint; and API key status, always shown as `not read or stored`.

Custom app path:

```bash
python3 zcode-keysmith.py install --zcode-app /path/to/ZCode.app --dry-run
python3 zcode-keysmith.py install --zcode-app /path/to/ZCode.app --yes
python3 zcode-keysmith.py verify --zcode-app /path/to/ZCode.app
# or
ZCODE_APP_PATH=/path/to/ZCode.app python3 zcode-keysmith.py install --dry-run
```

### Verification

```bash
python3 -m py_compile zcode-keysmith.py
python3 -m pytest tests -q
python3 zcode-keysmith.py install --dry-run
python3 zcode-keysmith.py doctor
python3 zcode-keysmith.py verify
```
