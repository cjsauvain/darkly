# 14 — CSRF (no Origin check)

**Where:** `POST /profile/me/settings`
**Flag:** `FLAG{csrf_4ny_0r1g1n_1s_w3lc0m3}`

## What is wrong

The "save profile" form has no CSRF token. I send the POST with an `Origin: https://example.com` header, a site that is not Darkly. The server accepts it and redirects to the settings page with `csrf_flag=FLAG{...}`.

Saving the profile changes the account. The server should check that the form was sent by our own page. It does not look at `Origin` or `Referer`, and the form has no hidden CSRF token.

`SameSite=Lax` makes a **browser** drop the cookie on a cross-site POST. That is useful, but it is not a server check. Any client that already has the cookie (curl, a proxy, an old browser, a subdomain of the same site) gets through.

## Exploit

```sh
sh exploit.sh
```

## Impact

If a victim's browser does send the cookie, an attacker's page can change their name, campus, and anything else this form writes.
On a bank site, the attacker's page could submit a transfer while the victim is still logged in.

## Fix

Check `Origin` (or `Referer`) on every POST and refuse other sites. Put a random CSRF token in the form and in the session, and compare them. Keep `SameSite=Lax` as a second layer, not the only one.
