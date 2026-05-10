---
name: Canvas admin creds — Dashlane pointer
description: Canvas site admin login for the Thufir self-hosted instance is stored in Dashlane under search term "canvas-homelab-admin".
type: reference
---

Canvas site-admin credentials for the self-hosted Canvas on Thufir (`canvas.wilsonidt.com`, LAN-only) are stored in Dashlane.

- **Dashlane search term:** `canvas-homelab-admin`
- **Admin email/login:** `admin@canvas.wilsonidt.com`
- **Created:** 2026-05-10 during setup.sh `db:initial_setup` step
- **Recovery if lost:** SMTP is not configured, so password reset email won't fire. Recovery path is Rails console: `docker compose run --rm web bin/rails console`, then update the user's password directly.

The Canvas `.env.canvas` `ENCRYPTION_KEY` and other Canvas secrets live in `~/claudeCode/projects/canvas-self-hosted/.env.canvas` on Thufir — never paste those into chat.
