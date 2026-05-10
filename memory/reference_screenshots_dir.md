---
name: Screenshots live in ~/Pictures/Screenshots
description: When Jefe says "check the screenshot" or "look at the latest screenshot," default location is /home/jeff/Pictures/Screenshots — sort by mtime and read the newest.
type: reference
---

When Jefe references a screenshot ("check the screenshot", "look at the latest one", etc.) without giving a path, look in:

`/home/jeff/Pictures/Screenshots/`

To find the most recent: `ls -t /home/jeff/Pictures/Screenshots/ | head -1`. Filenames follow the GNOME pattern `Screenshot from YYYY-MM-DD HH-MM-SS.png`. Read with the `Read` tool — it handles PNG visually.

This is the Thufir (Ubuntu/GNOME) convention. macOS machines (Alia, etc.) have screenshots on the Desktop by default; ask if unsure when working on a Mac.
