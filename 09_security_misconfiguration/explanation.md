# 09 — Security misconfiguration

**Where:** response headers on `/`, `/backup`, and the login `Set-Cookie`

## What is wrong

The app advertises how it is built and where the secrets live:

- `server` / `x-powered-by` — exact framework versions
- `x-pocketbase: http://localhost:8090` — where the admin console is
- `x-backup-exclude: data/private_notes.txt` — the file the path traversal reads
- session cookie without `HttpOnly` (and without `Secure`)

## Exploit

```sh
sh exploit.sh
```

There is nothing to "break". You read the headers and the cookie line.

## Impact

Each header removes a guess from another breach. The missing `HttpOnly` is what makes the stored XSS (breach 12) able to steal the bot's session **and** the flag cookie.

## Fix

Set cookies `HttpOnly; Secure; SameSite=Lax`. Do not put filesystem paths or internal hostnames in responses.
