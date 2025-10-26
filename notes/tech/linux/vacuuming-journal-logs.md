---
date: 2025-08-05
---
# Vacuuming Journal Logs

You can clean up Linux journal logs by ‘vacuuming’ them.

**How to manage the journal logs:**

1. **Check disk usage:** Use `journalctl --disk-usage` to see how much space the journal is consuming.

2. **Delete old logs (vacuuming):**

   * By size: `sudo journalctl --vacuum-size=100M` (example: remove logs until the total size is below 100MB).
   * By time: `sudo journalctl --vacuum-time=10d` (example: remove logs older than 10 days).
