## dedent

when creating long strings in functions use dedent so that we can indent the string content and make it more readable

for example:

```ts
import dedent from 'string-dedent'

const content = dedent`
  some content
```

IMPORTANT: notice that i have at start and end a new line. this is required when using string-dedent.

When creating code snippets alias dedent to variables like html or javascript so that I get syntax highlight in my editor: `const html = dedent`
