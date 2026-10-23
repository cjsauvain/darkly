# 13 — Path traversal

**Where:** `GET /projects/download?file=../private_notes.txt`
**Flag:** `FLAG{d0t_d0t_sl4sh_4ll_th3_w4y_d0wn}`

## What is wrong

On `/projects`, each PDF is `/projects/download?file=welcome.pdf`. Breach 09 names another file, `data/private_notes.txt`.

`file=private_notes.txt` is 404: the file is not next to the PDFs. `file=data/private_notes.txt` is 404 too: `data/` is not a subfolder of the PDFs. `file=../private_notes.txt` is 200. So the PDF folder is inside `data/`, and `private_notes.txt` sits directly in `data/`, one level above the PDFs.

You must be logged in. A guest does not reach this address.

## Exploit

```sh
sh exploit.sh
```

In the browser: Log in as benjamin (password set in breach 02), then open:

```text
/projects/download?file=../private_notes.txt
```

## Impact

A logged-in user can read files outside the PDF folder. The site opens those files with its own rights. Here the file is `private_notes.txt`, and the flag is inside.

## Fix

Join `file` to the PDF folder, resolve `..`, and refuse the download if the result is outside that folder.
Better: do not take a file name from the address. Give each PDF an id, and look the real name up in a table on the server.
