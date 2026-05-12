# AI-Debuggable Logging

A drop-in `CLAUDE.md` ruleset that makes AI coding assistants (Claude Code, Cursor, etc.) debug smarter — by writing logs that AI can actually read.

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

Works with Claude Code, Cursor, Windsurf, and any AI coding tool that reads `CLAUDE.md`.

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
