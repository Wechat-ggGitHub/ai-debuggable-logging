# AI-Debuggable Logging

A drop-in `CLAUDE.md` ruleset that makes AI coding assistants debug smarter — by writing logs that AI can actually read.

[中文说明](#中文说明)

---

## The Problem

When an AI assistant hits a bug, it guesses. It reads the code, forms a hypothesis, and makes changes — often the wrong ones. Sound familiar:

- AI changes 3 files to "fix" a bug that was a one-line typo
- You paste an error message, AI gives you 5 possible causes — none correct
- AI adds logging, but it's `console.log("here")` style gibberish

The root cause: **AI can't see what happened at runtime.**

## The Idea

Give AI a logging convention it can actually use:

- **Structured JSONL** — one JSON object per line, fixed fields, machine-parseable
- **Error-focused** — log what went wrong, not what went right
- **Timestamped directories** — each run gets its own folder, easy to compare
- **20-line implementation** — no frameworks, no dependencies, just a function

When AI hits a bug, it reads the logs first — sees the actual error, the actual inputs, the actual state — and fixes the real cause instead of guessing.

## The 4 Rules

| Rule | Summary |
|------|---------|
| **Read logs first** | Never touch code without reading logs. No logs? Add them to reproduce the bug. |
| **Verbose → Quiet** | New modules: log everything. Once stable (no new errors): only log failures. |
| **One JSON per line** | Fixed fields: `ts`, `level`, `mod`, `msg`. Errors get `err`, failed calls get `req`. |
| **No frameworks** | One function, ≤20 lines. Auto-creates timestamped directories. Add to `.gitignore`. |

## Usage

**Option 1: Copy to your project**

Append the rules to your project's `CLAUDE.md`:

```bash
# Chinese version
cat CLAUDE.md >> your-project/CLAUDE.md

# English version
cat CLAUDE.en.md >> your-project/CLAUDE.md
```

**Option 2: Use as a standalone CLAUDE.md**

If your project doesn't have a `CLAUDE.md` yet, just copy it in:

```bash
cp CLAUDE.en.md your-project/CLAUDE.md
```

## Example Log Output

```
logs/
└── 2024-01-15_14-30/
    ├── api.log
    ├── db.log
    └── auth.log
```

Each log file contains one JSON object per line:

```jsonl
{"ts":"2024-01-15T14:30:02Z","level":"info","mod":"auth","msg":"login attempt","req":"user_id=123"}
{"ts":"2024-01-15T14:30:02Z","level":"error","mod":"auth","msg":"token expired","err":"JWTExpiredError: ..."}
```

## Why Not...

| Alternative | Why it doesn't work for AI debugging |
|-------------|--------------------------------------|
| `console.log` / `print` | Unstructured, gets lost in stdout, AI can't parse it reliably |
| Winston / Pino / structlog | Overkill for debugging, requires config, AI doesn't need log levels or transports |
| APM tools (Datadog, etc.) | AI can't access your dashboard — logs need to be local files |
| Standard log files (`/var/log`) | Not in the project directory, AI might not have access |

## License

MIT

---

## 中文说明

一个直接复制到 `CLAUDE.md` 就能用的日志规则集，让 AI 编程助手通过结构化日志来调试，而不是靠猜。

### 问题在哪

AI 遇到 bug 时只能猜——读代码、假设原因、改代码，经常改错。根本原因：**AI 看不到运行时发生了什么。**

### 四条规则

| 规则 | 说明 |
|------|------|
| **先读日志再动代码** | 遇到 bug 先看日志，没有日志就先加日志复现，禁止盲猜 |
| **新模块多记，稳定后只记错** | 首次接入时每个环节都记，稳定后只在错误路径写日志 |
| **一行 JSON，固定字段** | 必填 `ts`、`level`、`mod`、`msg`，error 带 `err`，外部调用失败带 `req` |
| **够用就行，不搞框架** | 一个写 JSONL 的函数，不超过 20 行，按启动时间自动建目录 |

### 使用方法

```bash
# 中文版
cat CLAUDE.md >> your-project/CLAUDE.md

# 英文版
cat CLAUDE.en.md >> your-project/CLAUDE.md
```

### 日志示例

```
logs/
└── 2024-01-15_14-30/
    ├── api.log
    ├── db.log
    └── auth.log
```

```jsonl
{"ts":"2024-01-15T14:30:02Z","level":"info","mod":"auth","msg":"login attempt","req":"user_id=123"}
{"ts":"2024-01-15T14:30:02Z","level":"error","mod":"auth","msg":"token expired","err":"JWTExpiredError: ..."}
```
