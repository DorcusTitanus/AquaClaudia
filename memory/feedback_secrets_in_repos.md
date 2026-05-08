---
name: Secrets stay out of chat AND out of git
description: Standing rule: credentials live in local .env files, gitignored before any credential is written. Never paste secrets into chat (Anthropic sees tool output and conversation transcripts).
type: feedback
originSessionId: c277d2fb-213a-4cdf-9509-14fde273a074
---
When any project on Thufir needs an auth token, API key, password, or
similar secret:

1. **Don't paste it in chat.** Tool inputs/outputs and conversation text
   go to Anthropic. Have Jefe drop it into a local file in his terminal.
2. **Use a `.env` file** in the project root. Reference via env vars in
   code, not hardcoded strings.
3. **Add to `.gitignore` BEFORE writing the secret to disk.** This is
   the order that prevents accidents.

**Why:** Jefe is secops-minded. AquaClaudia is public. A push of a `.env`
to a public repo = credentials in GitHub history forever, scraped within
minutes. Same for any repo Jefe owns.

**How to apply:** When scaffolding a new repo or adding a new credential
to an existing one, the standard secrets block (copy verbatim from
`~/claudeCode/projects/AquaClaudia/.gitignore`):

```
# Never commit secrets
*.env
*.key
*.pem
credentials.*
infrastructure.md
```

The `infrastructure.md` line is Jefe's convention for "the unsanitized
counterpart" — anywhere AquaClaudia is public/sanitized, an
`infrastructure.md` may exist locally with the real paths/IPs/credentials.
That filename is reserved and never committed.

Verify before adding any credential: `cat .gitignore | grep -E '\.env|secret'`.
If the repo doesn't have it, add the block first, commit the .gitignore
update, then create the .env.
