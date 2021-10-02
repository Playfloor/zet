# Learn to Use `replace` in `go.mod`

📺 <https://youtu.be/k_nx_zPf84A>

The `replace` keyword in `go.mod` allows you to work on several
packages, with different repos, at the same time, or, to test changes
locally before pushing them.

Unfortunately, it is rather easy to leave the `replace` in your `go.mod`
file and forget and commit it. Until rather recently, that meant other
projects depending on your module would fail to compile trying to locate
your local path on their build machines. These days `go` won't even
compile or install the module if there is a `replace` in the `go.mod`
file.

Even so, committing to a production repo with `replace` could be
embarrassing so might want to add the following `.git/hooks/pre-commit`
hook (don't forget to `chmod +x`) to prevent any commit of `go.mod` that
has the word replace in it, which you could easily modify to check that
it isn't commented out (`// replace` should be allowed). This script
checks for `jq` and if not found uses `grep` instead:


```bash
#!/bin/bash

jq=$(command -v jq)
if [[ -n "$jq" ]] ; then
  rv="$(go mod edit -json | $jq .Replace)"
  [[ $rv = 'null' ]] && exit 0
  echo "replace found in go.mod"
  exit 1
fi

git diff --cached go.mod | grep replace && exit 1
```

Tags:

    #golang #tips #replace
