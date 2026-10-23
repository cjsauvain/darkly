# 11 — Unrestricted file upload

**Where:** Profile settings → Upload image
**Flag:** `FLAG{unr3str1ct3d_upl0ad_g0_brrr}`

## What is wrong

Logged in as a student, the profile page has an avatar upload: send a small PHP file, the server accepts it and shows "Unrestricted upload accepted" with the flag. It checked neither the extension nor the contents.

The payload in `Resources/avatar.php` is one line:

```php
<?php echo "poc";
```

## Exploit

```sh
sh exploit.sh
```

In the browser: Profile settings → choose `avatar.php` → Upload. The form posts to `/upload/avatar`. The flag is in the redirect: `upload_flag=FLAG{...}`.

## Impact

If the server also **executes** that file, this is code execution. Even if it only stores it, users can upload content the application never meant to accept.

## Fix

Allow-list extensions (`jpg`, `png`) **and** check the real file signature, not the browser's `Content-Type`.
