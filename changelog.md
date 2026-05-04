# changesets

After completing a fix or feature for a **public package** (has `version` in package.json, not `private: true`), add a changeset file in `.changeset/` at the repo root. Never edit CHANGELOG.md directly; it is generated automatically at publish time when changesets are consumed.

Never run the `changeset` CLI command interactively. Always create the file manually.

Create a `.md` file with a random kebab-case name (e.g. `cool-lions-dance.md`):

```md
---
'package-name': patch
---

Description of what changed, with code examples if applicable.
```

Multiple packages can be listed in one changeset:

```md
---
'spiceflow': minor
'create-spiceflow': patch
---

Add federation support for remote RSC components.
```

## rules

- **Never use `major`.** Use `patch` for fixes and `minor` for new features.
- **Never edit CHANGELOG.md directly.** It is generated from changesets at publish time.
- **Never bump `package.json` version manually.** Versions are bumped automatically.
- **Never run the changeset CLI.** Write the `.md` file yourself.
- **Only public packages.** Skip changesets for packages marked `private: true` or without a `version` field.
- **Present tense.** Write "add support for X", "fix bug with Y".
- **One changeset per logical change.** Two unrelated changes get two files.

## rich content

Changeset descriptions become the public changelog. Write them as rich content aimed at end users:

- Code examples showing new APIs or changed behavior
- Migration steps if the user needs to update their code
- Diagrams explaining architecture changes
- Before/after comparisons

## private packages

For **private packages** (`private: true`, no version), skip changesets entirely. These do not get published.

Load the `changesets` skill for full workflow details.
