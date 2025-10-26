---
title: Awk & Sed Cookbook
date: 2020-09-10
---

## Awk Cookbook

### Special variables

- NF: number of fields (for the current line)
- NR: number of records (lines) read so far. This keeps increasing across multiple files.
- FNR: number of records in current file read so far. This resets to 1 when the next file is processed.

### Basic grep for pattern

```bash
awk '/pattern/ {print}'` or even simpler `awk '/pattern/'
```

### Replace pattern with string

```bash
awk '{ sub(/pattern/,"foobar") } 1'   # note the '1' at the end
```

### Some awk examples

```bash
awk 'NR % 6'            # prints all lines except those divisible by 6
awk 'NR > 5'            # prints from line 6 onwards (like tail -n +6, or sed '1,5d')
awk '$2 == "foo"'       # prints lines where the second field is "foo"
awk 'NF >= 6'           # prints lines with 6 or more fields
awk '/foo/ && /bar/'    # prints lines that match /foo/ and /bar/, in any order
awk '/foo/ && !/bar/'   # prints lines that match /foo/ but not /bar/
awk '/foo/ || /bar/'    # prints lines that match /foo/ or /bar/ (like grep -e 'foo' -e 'bar')
awk '/foo/,/bar/'       # prints from line matching /foo/ to line matching /bar/, inclusive
awk 'NF'                # prints only nonempty lines (or: removes empty lines, where NF==0)
awk 'NF--'              # removes last field and prints the line
awk '$0 = NR" "$0'      # prepends line numbers (assignments are valid in conditions)

# common pattern when processing 2 or more files
awk 'NR==FNR { # some actions; next } # other condition {# other actions}' file1 file2
# prints lines that are both in file1 and file2 (intersection)
awk 'NR==FNR { a[$0];next } $0 in a' file1 file2
```

### Pass data to awk

- Awk runs on multi-line text input. You can either pipe or redirect input into awk like many Unix programs.
- Shell variables can be passed into awk via the `-v` option. Note that they must be strings. To pass in arrays, decide on a delimiter and split on that delimiter in awk.

```bash
... | awk -v myvar="$SOMEVAR" '{
  printf "%s", myvar
}'
```

### Get unique values of a column

```bash
awk '{ a[$1]++ } END { for (x in a) { print x }'
```

### Resources

- https://catonmat.net/ten-awk-tips-tricks-and-pitfalls


## Sed Cookbook

_TO DO_
