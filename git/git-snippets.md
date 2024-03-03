# git snippets

## alises

### gitql

colored and full alternative to git log --oneline
```bash
alias gitql="git log --pretty=format:"%C(yellow)%h%C(reset) %C(green)%ad%C(reset) %C(blue)%an%C(reset): %s" --date=short"
```

## check people
```bash
git log --format='%aN <%aE>' | sort -u
```

## check update changes in some path
to get info on specific files in a certain directory (version number):
```bash
git diff <old_commit>..HEAD -- <directory> -- *<suffix>
git diff abc123..HEAD -- /subjects/path/version/ -- *.pl.md

```

or across versions (with main pill directory and CCC to track moved files)

```bash
git diff -C -C -C <old_commit>..HEAD -- <directory> -- *<suffix>
```

## check new PL changes

```bash
git diff --word-diff HEAD 14a5b6 -- "subjects/Prework/Przygotowanie do kursu/4.1/" --not --author="krzysiek.wie@gmail.com"
```

```bash
git diff --name-status 14a5b6 HEAD -- "subjects/Prework/Przygotowanie do kursu/4.1" | grep -vE '\.en\.|\.yml$'
```
