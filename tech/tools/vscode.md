---
title: Notes on VS Code
date: 2022-10-24

tags:
- vscode
---

## VS Code Extensions

### Redundant Extensions

You don't really need these extensions anymore as VS Code has built in similar functionality.

#### Auto Rename Tag

Unless you are editing JXSX/Vue files.

```json
"editor.linkedEditing": true
```

### Auto Close Tags

```json
"html.autoClosingTags": true,
"javascript.autoClosingTags": true,
"typescript.autoClosingTags": true
```

### Auto Import

```json
"javascript.suggest.autoImports": true,
"typescript.suggest.autoImports": true,
"javascript.updateImportsOnFileMove.enabled": "always",
"typescript.updateImportsOnFileMove.enabled": "always",
"source.organizeImports": true
```

### Settings Sync

You can now sync with your GitHub account. See [official documentation](https://code.visualstudio.com/docs/editor/settings-sync)

### Trailing Spaces

There is a setting now to automatically trailing spaces remove them on save. Unless you still like to see them highlighted.

### Path Intellisense / Npm Intellisense

Built-in feature is turned on by default.

### npm

By Microsoft, now deprecated.

### HTML Snippets / CSS Snippets / HTML Boilerplate

VS Code now has Emmet built-in.

### HTMLTagWrap

Use the built-in Emmet.

### Lorem Ipsum

Supported by Emmet. Just type 'lorem' and press Tab.

### Bracket Pair Colorizer

The built-in one is much faster.

```json
"editor.bracketPairColorization.enabled": true
```
