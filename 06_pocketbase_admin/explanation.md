# 06 — PocketBase admin (become the administrator)

**Where:** `http://localhost:8090/_/` and the admin API
**Flag:** `FLAG{th3_und3rsc0r3_sl4sh_kn0ws_th3_w4y}`

## What is wrong

The previous step gave me `admin@42network.local` / `Darkly42Admin!`. The `x-pocketbase` header says the admin is on port 8090. I log in. I am admin of the whole database. The `internal_audit` collection contains the flag. This is the highest privilege in the application: I can read and modify every user.

## Exploit

```sh
sh exploit.sh
```

In the browser: open `http://localhost:8090/_/`, log in with those two values, open the collection **internal_audit**.

## Impact

Total database compromise: every user, every grade, every private note. From here you can also set your own app role to `god`.

## Fix

Do not expose PocketBase on port 8090. Only the website should reach it.
Do not put its admin password in the /internal/config JSON response. Keep that password in a server-side secret.
