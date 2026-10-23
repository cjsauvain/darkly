# Insecure Direct Object Reference

**Where:** `GET /profile/<id>` with no session
**Flag:** `FLAG{1d0r_ur_pr0f1l3_1s_m1n3}`

## What is wrong

On the forum, wil's pinned post has a "View profile" link. Open that link without logging in. The private note, which says it is only visible to wil, contains the flag.

## Exploit

```sh
sh exploit.sh
```

One request, no cookie: `GET /profile/k1asdfeditojrb4`. That id is the "View profile" link on wil's pinned post.

## Impact

Anyone can read private notes from any user profile.

## Fix

Check the user associated with the session cookie. If that user is the profile owner or an admin, show the private note. Otherwise, hide it.
