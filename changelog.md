# changelog

## 1.1.2

### Patch Changes

- Header comment in generated AGENTS.md instructing not to edit directly
- Instructions to create ./MY_AGENTS.md for custom instructions

after you make a change that is noteworthy, add an entry in the CHANGELOG.md file in the root of the package. there are 2 kinds of packages, public and private packages. private packages have a private: true field in package.json, public packages do not and instead have a version field in package.json. public packages are the ones that are published to npm.

If the current package has a version field and it is not private then include the version in the changelog too like in the examples, otherwise use the current date and time.

If you use the version you MUST use a bumped version compared to the current package.json version, and you should update the package.json version field to that version. But do not publish. I will handle that myself.

to write a changelog.md file for a public package, use the following format, add a heading with the new version and a bullet list of your changes, like this:

```md
## 0.1.3

### Patch Changes

- bug fixes

## 0.1.2

### Patch Changes

- add support for githubPath
```

for private packages, which do not have versions, you must instead use the current date and time, for example:

```md
# Changelog

## 2025-01-24 19:50

- Added a feature to improve user experience
- Fixed a bug that caused the app to crash on startup
```

these are just examples. be clear and concise in your changelog entries.

use present tense. be detailed but concise, omit useless verbs like "implement", "added", just put the subject there instead, so it is shorter. it's implicit we are adding features or fixes. do not use nested bullet points. always show example code snippets if applicable, and use proper markdown formatting.

```

the website package has a dependency on docs-website. instead of duplicating code that is needed both in website and docs-website keep a file in docs-website instead and import from there for the website package.
