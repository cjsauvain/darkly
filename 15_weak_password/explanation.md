# 15 — Weak password, stored as unsalted MD5

**Where:** staff dashboard column `pw_hint`, then `Resources/passwords.txt`
**Flag:** none. This is the extra vulnerability.

## What is wrong

From breach 04, the staff dashboard shows jdoe's `pw_hint`: `e99a18c428cb38d5f260853678922e03`. That string is `md5("abc123")`. The hint is the password hash, with no salt. `abc123` is in the common-password list.

The same word always gives the same MD5, because no salt is added first.

Breach 10 already found `abc123` by trying the list on `/login`. This breach is the other path. With only the hint from breach 04, hash each line of `Resources/passwords.txt` (the same SecLists file as breach 10) and keep the line whose MD5 equals `pw_hint`.

## Exploit

```sh
sh exploit.sh
```

Logged in as benjamin, the script reads jdoe's `pw_hint`, hashes each line of `Resources/passwords.txt`, and prints the line that matches. That line is the password.

## Impact

Anyone who can read the hint can recover the password on their own machine. A weak password plus a readable unsalted MD5 is enough to take the account.

## Fix

Store passwords with `password_hash()` (bcrypt or argon2): slow, and salted. Delete the hint, or make it a real hint that is not derived from the password.
