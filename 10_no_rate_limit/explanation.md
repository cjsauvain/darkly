# 10 — No rate limit on login

**Where:** `POST /login`

## What is wrong

I try multiple passwords, one after another, as `jdoe`. Every failure gets the same answer. No 429 (too many requests), no account lock, no captcha.
There is no limit on the number of attempts. That is why a list can be used: the server never stops the next try. The list runs until a password is accepted.

## Exploit

```sh
sh exploit.sh
```

The list is `Resources/passwords.txt`. It is SecLists' `10k-most-common.txt`, from [danielmiessler/SecLists](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10k-most-common.txt): the 10,000 passwords most often seen in leaked credential lists. The script does not contain the password. It tries that file until one login is accepted.

## Impact

Online password guessing is practical. Nothing slows the attempts, so a list of common passwords can be tried until one logs in.

## Fix

After a few wrong passwords, wait or lock the account. Do the same for the IP. Write the failures in a log a person reads. Keep the message "invalid credentials", so it does not say if the name or the password was wrong.
