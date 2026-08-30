---
name: notes
description: |
  Apply when a task could draw on Alex's personal notes — his Obsidian
  vault syncs to this server as plain markdown, read-only, at
  ~/services/notes/vault/. Mostly creative projects. Also apply before
  touching ~/services/notes (CouchDB + livesync-bridge).
---

# Alex's notes (Obsidian vault)

Read-only copy of the vault: `~/services/notes/vault/` — plain markdown.
Search with `rg`, read files directly. Read surgically; don't bulk-load.

The copy is live: devices ↔ CouchDB (`couchdb` container) ↔
`livesync-bridge` container ↔ the folder. Writes are blocked by file
permissions (the bridge container owns the folder). This is deliberate:
an accidental write here would propagate to every one of Alex's devices.
Do not work around it; if a task truly needs to write a note, stop and
ask Alex.

If the folder looks stale or empty: `docker logs livesync-bridge`.
Credentials and the encryption passphrase: `~/services/notes/.env`
(copied into `bridge/dat/config.json`, which the tool reads).
