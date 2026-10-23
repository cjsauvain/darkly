# 03 — Hidden API (`/api/grades`)

**Where:** `GET /api/grades?student=<id>` (the path is named in `robots.txt`)
**Flag:** `FLAG{md5_1s_4_n4m3pl4t3_n0t_4_l0ck}`

## What is wrong

`robots.txt` asks crawlers not to visit `/api/grades`. Logged in as benjamin, that URL returns only his grades, and their `flag` fields are empty. Each row has a `student` id. jdoe's id is the profile link on his forum post, `/profile/z4p1cnx47mfy50f`. I open `/api/grades?student=z4p1cnx47mfy50f`. His Darkly grade contains the flag.

## Exploit

```sh
sh exploit.sh
```

In the browser, logged in as benjamin (breach 02):

1. Forum → jdoe's post → **View profile**. The address is `/profile/z4p1cnx47mfy50f`.
2. Open `http://localhost:4942/api/grades?student=z4p1cnx47mfy50f`.

`/api/grades` with no parameter is benjamin's own grades. The flag is not there.

## Impact

Private academic data (and the flag) is readable by any logged-in user.

## Fix

Ignore `student` unless it is the logged-in user, or unless the caller is staff. Remove the flag from the payload.
