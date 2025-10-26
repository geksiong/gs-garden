---
title: Shell Scripting Cookbook
date: 2020-09-11

tags:
  - bash
  - zsh
  - shell
---

# My Shell Scripting Cookbook

## Bash

### Command-line arguments

- Arguments are contained in `$@`.  Access them with `$1, $2, $3...`.
- `$0` is the program being run
- File globs are expanded into multiple args.

### Arrays

- Define arrays as `( e1, e2, e3, ... )`
- Access array elements using `$arr[1], $arr[2]`
- To loop over array elements, use `for e in ${arr[@]}; do ...`

### Multi-line string output of program into array

Firstly, redefine the "Internal Field Separator" as newline only (default is space, tab, newline `IFS=$' \t\n'`)

```sh
IFS=$'\n'
$arr = ( $(command) )
for e in ${arr[@]}; do ...
```

### Echo without newline

`echo -e ...`

### Does file exist?

### Does variable exist?

### Functions

### Get script directory

### Get Exit code

## Differences between bash and zsh
