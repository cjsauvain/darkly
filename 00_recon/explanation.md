# 00 — Recon

**Part:** start here. This is not a vulnerability and it has no flag.



Before attacking, read what the site gives away on its own: the home page, the HTTP headers, and `robots.txt`.

```sh
sh exploit.sh
```

Three things matter:

1. **Headers.** `server` and `x-powered-by` name the stack (Python, FastAPI, uvicorn). `x-pocketbase` points at the database admin on port `8090`.
2. **HTML comments.** The deploy notes mention bugs the developers left in on purpose (XML parser, cookies, telemetry).
3. **`robots.txt`.** It lists `/admin`, `/staff`, `/internal`, `/backup`, `/api/grades`, `/_/`. A `Disallow` is not a lock. It only asks nice crawlers to stay away.

## Why it matters

Every later breach starts from one of these lines. The grades flag is the `/api/grades` line. The admin database is the PocketBase line. The XXE hint is in the HTML comments and on the staff page.
