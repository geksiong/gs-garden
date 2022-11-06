---
title: Git Guidelines
date: 2022-10-24

tags:
- git
---

## Git Guidelines


### Branches

- `main` branch is the primary development branch
- `prod` branch is the production release branch

*more to come...*

### Commits

- Adopt [Conventional Commits](https://www.conventionalcommits.org) for commit message syntax
- Commit messages should be prefixed with one of the following:
  - `fix:` fixes bugs
  - `feat:` adds new features
  - `chore:` makes general changes
  - `ci:` contains changes to continuous integration / continuous deployment
  - `refactor:` improves existing codes
- Start with lowercase character
- Use present tense
- Include issue number
- Indicate if the commit is still a work in progress
- Do provide more details in the commit body, if it helps to clarify.

#### Examples

`feat: [PROJX-100][WIP] implement search function`
