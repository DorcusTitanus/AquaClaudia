---
name: Docker Compose env_file parsing gotchas — $, #, and restart-vs-recreate
description: Docker Compose's env_file parser interprets `$` as variable expansion and `#` as comment. Use double-quoted values + `$$` to escape, or restrict to alphanumeric. Also: `docker compose restart` does NOT reload env_file; you must recreate the container.
type: feedback
---

When configuring secrets / credentials via Docker Compose `env_file` (`.env.canvas`, `.env`, etc.):

**Special-character pitfalls:**

- `$` triggers variable expansion. A password with `$` in it (e.g. `abc$xyz123`) gets read as `abc` plus a lookup of `$xyz123`. If that variable isn't defined, it expands to empty string and the final value is just `abc`. Symptom: container sees a truncated password.
- `#` starts an inline comment. `KEY=abc#def` reads as `KEY=abc`. Same truncation symptom.
- ``` ` ```, `\`, `"`, `'` — similar interpretation risk depending on Compose version.

**Two fixes:**

1. Double-quote the value AND escape `$` as `$$`: `KEY="abc$$xyz"`.
2. Generate passwords with restricted character set: alphanumeric + safe specials (`-_.,:`) only. Cleaner — works across `.env` files, YAML, JSON, shell, anywhere, without escape juggling. Dashlane (and most password managers) can be configured to exclude shell-special chars.

**`restart` vs `up -d --force-recreate`:**

- `docker compose restart <svc>` restarts the *process* inside an *existing* container. Env vars were baked at container-create time — they do NOT refresh.
- To pick up new `env_file` values, the container must be **recreated**:
  - `docker compose up -d --force-recreate web job` — recreates the named services with current env values. Targeted, doesn't disturb other services.
  - `docker compose down && docker compose up -d` — heavier; restarts everything (incl. postgres/redis).

**Diagnostic pattern (without leaking the secret to chat):**

Verify the container has the expected value by checking length only, never raw value:

```ruby
docker compose exec web bin/rails runner 'p = ENV["SECRET_VAR"]; puts "len=#{p&.length}"'
```

Length mismatch = parsing problem (try escaping or rotating). Length match but auth still fails = real auth issue (creds wrong, IP block, username format).

**Observed 2026-05-11 during Canvas SMTP setup:**
- First rotation: password contained `$` → container saw 3-char truncated value → auth failed.
- After force-recreate with alphanumeric-only password → SMTP working end-to-end.
- Initial `docker compose restart web job` left stale env vars in container; only `up -d --force-recreate` picked them up.
