# zcode-keysmith

<p align="center">
  <strong>ZCode App managed true system-role entrypoint.</strong>
</p>

<p align="center">
  <a href="README.md">简体中文</a> ·
  <a href="#english">English</a> ·
  <a href="docs/reference.md">Reference</a> ·
  <a href="docs/agent-install.md">Agent install</a> ·
  <a href="LICENSE">License</a>
</p>

## English

### What this is

`zcode-keysmith` installs a managed true system-role entrypoint for the local ZCode desktop app. It installs the repository's `examples/system-role.md` as a managed copy in the user directory, and routes it into ZCode's runtime `customSystemPrompt` path so a newly started agent-server process picks it up as a system message. **The ZCode app bundle remains untouched**: the installer only writes to the user directory and a user LaunchAgent. API keys, provider settings, MCP settings, and project files stay under ZCode's own management; the installer never reads, stores, or prints them.

Default managed files:

```text
~/.zcode-keysmith/system-role.md
~/.zcode-keysmith/config.json
~/.zcode-keysmith/bin/zcode-agent-wrapper.py
~/.zcode-keysmith/bin/zcode-keysmith-env.sh
~/Library/LaunchAgents/com.jia.zcode-keysmith.env.plist
```

### How it works

The ZCode desktop app reads two environment variables when starting agent-server:

```text
ZCODE_AGENT_SERVER_COMMAND
ZCODE_AGENT_SERVER_ARGS_JSON
```

`zcode-keysmith` points `ZCODE_AGENT_SERVER_COMMAND` at `~/.zcode-keysmith/bin/zcode-agent-wrapper.py`. The wrapper does three things:

1. Reads ZCode's bundled runtime: `/Applications/ZCode.app/Contents/Resources/glm/zcode.cjs`;
2. Caches a copy of the runtime in the user directory, patching only the `customSystemPrompt` entrypoint to prefer `~/.zcode-keysmith/system-role.md`;
3. Launches the cached runtime with ZCode's own Electron node command, preferring the bundled `ZCode Helper` executable so the background agent-server is not shown as another foreground ZCode app in the Dock; it falls back to the main executable only when no Helper binary is present.

The ZCode runtime places `customSystemPrompt` into an `injectionTarget: "system"` context segment, so `system-role.md` lands in ZCode's system message path rather than as an ordinary project instruction file. During install, the source prompt is normalized once: if `examples/system-role.md` comes from a GLM ChatML export, the outer `<|im_start|>system:` / `<|im_end|>` transport markers are cleaned, and only the system prompt body is written.

### Install

```bash
python3 zcode-keysmith.py install --dry-run   # preview
python3 zcode-keysmith.py install --yes        # confirm and write
python3 zcode-keysmith.py doctor               # check state
python3 zcode-keysmith.py verify                # check the wiring
```

Existing managed files are backed up as `<filename>.bak_YYYYMMDD_HHMMSS`. `--dry-run` takes precedence even if `--yes` is also passed.

The installer updates the current macOS launchd environment and writes a user LaunchAgent so subsequent login sessions restore the same variables. **Any already-open ZCode process must be reopened** to inherit the new agent-server entrypoint. After install:

1. Quit and reopen ZCode;
2. Start a new task, type "Who are you?";
3. Run `python3 zcode-keysmith.py verify`; `wrapper_invoked: true` confirms ZCode actually launched the managed wrapper.

Full field reference and `doctor`/`verify` output: [`docs/reference.md`](docs/reference.md).

### Uninstall

```bash
python3 zcode-keysmith.py uninstall --dry-run   # preview
python3 zcode-keysmith.py uninstall --yes        # confirm removal
```

Renames managed files to `.bak_YYYYMMDD_HHMMSS` and clears the keysmith entrypoint from the current launchd environment. The ZCode app bundle remains untouched.

### ZCode at a non-default path

```bash
python3 zcode-keysmith.py install --zcode-app /path/to/ZCode.app --dry-run
# or
ZCODE_APP_PATH=/path/to/ZCode.app python3 zcode-keysmith.py install --dry-run
```

### Project layout and verification

See [`docs/reference.md`](docs/reference.md) for the project layout and `py_compile`/`pytest` verification steps.

---

简体中文版: [`README.md`](README.md)。Agent install prompt and sibling projects: [`docs/agent-install.md`](docs/agent-install.md).
