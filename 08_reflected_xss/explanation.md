# 08 — Reflected XSS (newsletter)

**Where:** `GET /newsletter?email=<script>alert(1)</script>&msg=subscribed`

## What is wrong

The newsletter page pastes the `email` parameter into the "subscribed with" banner. The box of the form is escaped. The banner is not. I put `<script>alert(1)</script>` in `email`. The browser runs it. The session cookie is not `HttpOnly`, so this script can read `document.cookie`.

**XSS** means the site treats your text as part of the page, so the browser runs it.

**Reflected** means it happens immediately. You put the text in the address, the server sends the page back with that text inside it.

**Escaping** means turning `<` into harmless text, so the browser displays it instead of running it. The email box on the form does this. The banner "subscribed with" does not. It inserts `email` into the HTML as-is.

The session cookie is not `HttpOnly` (breach 09). `document.cookie` is the list of cookies JavaScript can read. A script on this page can read the session.

## Exploit

```sh
sh exploit.sh
```

The script requests the newsletter URL and checks that `<script>alert(1)</script>` comes back unchanged. That proves the banner does not escape the text.

To see it run, open this address in the browser:

```text
http://localhost:4942/newsletter?email=<script>alert(1)</script>&msg=subscribed
```

A window says `1`. Click OK. The banner contains the script. If the window does not open, the text `<script>alert(1)</script>` visible in the banner is already the proof.

Log in first if you want `alert` to show the session cookie. The hole itself does not need an account.

## Impact

You send that address to someone who is logged in. Their browser runs your JavaScript inside their session. With the missing `HttpOnly` (breach 09), the script reads `document.cookie` and can take their session.

## Fix

Escape every value written into the page, including the banner. Set the session cookie `HttpOnly`, so JavaScript cannot read it even if a script runs.
