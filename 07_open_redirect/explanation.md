# 07 — Open redirect

**Where:** `GET /redirect?next=https://example.com`

## What is wrong

The site's redirect link takes the address from `next` and puts it straight into `Location`. I set `https://example.com`, and the server answers 307 to that site. A real attacker would put a phishing page that looks like Darkly.

## Exploit

```sh
sh exploit.sh
```

Or open this and watch the address bar:

```text
http://localhost:4942/redirect?next=https://example.com
```

## Impact

A link that **starts** with the real Darkly host can still land on an attacker's site.
Useful for phishing, and sometimes for leaking tokens that get attached to the redirect.

## Fix

Only allow next to be a path on this site, such as /forum. Link to other websites with a normal link, not with next.
