# AI-Debuggable Logging

Let AI see what actually happened at runtime instead of guessing. Logs serve AI diagnosis, not ops completeness.

---

**Read logs before touching code**

When you hit a bug, check `logs/YYYY-MM-DD_HH-MM/` first (auto-created per startup, files split by module). No logs? Add logging to reproduce the issue before fixing. Never blind-guess. After fixing, confirm the error no longer appears in logs.

**Log everything for new modules, only failures once stable**

On first integration, log at every key step (input params, intermediate state, external calls, branch decisions) — treat it as a runtime manual for AI. Consider it stable when it runs with no new errors; then downgrade or remove extra info logs. Once stable, only log on error paths. Keep happy paths silent — noise is worse than no logs.

**One JSON per line, fixed fields**

Each log entry is one line of JSON, e.g.: `{"ts":"2024-01-01T00:00:00Z","level":"error","mod":"auth","msg":"token expired","err":"<stack>"}`. Required: `ts` (ISO 8601), `level`, `mod`, `msg`. Errors must include `err` (stack trace). Failed external calls must include `req` (input). No free-text log messages.

**Keep it minimal, no frameworks**

One JSONL writer function, no more than 20 lines. It auto-creates a timestamped directory on each run. Add logs when you encounter a bug, not all at once. Add the log directory to `.gitignore`.
