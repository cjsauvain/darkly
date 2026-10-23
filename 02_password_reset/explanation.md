# 02 — Password reset - `md5(email)`

**Where:** `/reset-password?email=...&token=...`
**Flag:** `FLAG{r3s3t_t0k3n_w4s_just_md5_lol}`

## What is wrong

Benjamin wrote on the forum that the reset token is the MD5 of his email. His email is public. I compute `md5(benjamin@student.42.tech)`, I request the reset page with that token, I set a new password, I log in. On `/profile/me/settings`, the recovery code is the flag.

Checked by hand:

```text
md5("benjamin@student.42.tech") = f1640a02eeccb971463836da7300b3ba
```

That is the `token=` value the site puts in the reset URL.

## Exploit

```sh
sh exploit.sh
```

In the browser, without the script:

1. Forum → benjamin's email `benjamin@student.42.tech`.
2. Go to the login page
3. Click on "Forgot your password ?"
4. Write benjamin's email and click on "Send reset link".
5. It redirects to the reset page with the reset token already provided in the url.
6. Write any new password.
7. Login with this new password and go to benjamin's settings.
8. Scroll down to get the Flag.

## Impact

Account takeover of every user whose email you know. No access to their mailbox.

## Fix

Generate a random reset token and store it server-side. Send it by email only, and delete it after one use.
