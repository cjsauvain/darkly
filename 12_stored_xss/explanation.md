# 12 — Stored XSS (moderation bot)

**Where:** `POST /forum/new`, body `Resources/payload`
**Flag:** `FLAG{xss_st0r3d_1s_n0t_4_f34tur3_w1l}`

## What is wrong

The forum says a moderation bot opens every new post within a minute. The new-post form says HTML is allowed in the body, so
a `<script>` in the post runs in the browser.

**Stored** means the site saves the script in the post and sends it again to every later visitor.

## Exploit

```sh
sh exploit.sh
```

Logged in as benjamin, I create a post whose body is `Resources/payload`. The bot opens that post, the script runs in its browser, and it posts its own `document.cookie` back as a comment. That cookie is not `HttpOnly` and contains `flag=FLAG{...}`.

The post body is saved and later sent back as HTML. Every visitor who opens the post runs the script: me, another student, the bot or and admin.

In the browser: Forum → **New post** → paste `Resources/payload` into **Content** → **Post**. Reload the new thread. The flag is in a comment.

## Impact

Whoever opens the post runs the script in their session. Here the visitor that matters is the moderation bot, so its cookie is the prize. The same bug against a staff user would steal that user's session.

## Fix

Store the post body and the comments as text, and escape them when displaying the page. Set `HttpOnly` on every cookie that matters, so JavaScript cannot read it. Add a Content-Security-Policy that blocks inline scripts.
