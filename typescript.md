# typescript

- ALWAYS use normal imports instead of dynamic imports, unless there is an issue with es module only packages and you are in a commonjs package (this is rare).

- use a single object argument instead of multiple positional args: use object arguments for new typescript functions if the function would accept more than one argument, so it is more readable, ({a,b,c}) instead of (a,b,c). this way you can use the object as a sort of named argument feature, where order of arguments does not matter and it's easier to discover parameters.

- always add the {} block body in arrow functions: arrow functions should never be written as `onClick={(x) => setState('')}`. NEVER. instead you should ALWAYS write `onClick={() => {setState('')}}`. this way it's easy to add new statements in the arrow function without refactoring it.

- in array operations .map, .filter, .reduce and .flatMap are preferred over .forEach and for of loops. For example prefer doing `.push(...array.map(x => x.items))` over mutating array variables inside for loops. Always think of how to turn for loops into expressions using .map, .filter or .flatMap if you ever are about to write a for loop.

- if you encounter typescript errors like "undefined | T is not assignable to T" after .filter(Boolean) operations: use a guarded function instead of Boolean: `.filter(isTruthy)`. implemented as `function isTruthy<T>(value: T): value is NonNullable<T> { return Boolean(value) }`

- minimize useless comments: do not add useless comments if the code is self descriptive. only add comments if requested or if this was a change that i asked for, meaning it is not obvious code and needs some inline documentation. if a comment is required because the part of the code was result of difficult back and forth with me, keep it very short.

- ALWAYS add all information encapsulated in my prompt to comments: when my prompt is super detailed and in depth, all this information should be added to comments in your code. this is because if the prompt is very detailed it must be the fruit of a lot of research. all this information would be lost if you don't put it in the code. next LLM calls would misinterpret the code and miss context.

- NEVER write comments that reference changes between previous and old code generated between iterations of our conversation. do that in prompt instead. comments should be used for information of the current code. code that is deleted does not matter.

- use early returns (and breaks in loops): do not nest code too much. follow the go best practice of if statements: avoid else, nest as little as possible, use top level ifs. minimize nesting. instead of doing `if (x) { if (b) {} }` you should do `if (x && b) {};` for example. you can always convert multiple nested ifs or elses into many linear ifs at one nesting level. use the @think tool for this if necessary.

- typecheck after updating code: after any change to typescript code ALWAYS run the `pnpm typecheck` script of that package, or if there is no typecheck script run `pnpm tsc` yourself

- do not use any: you must NEVER use any. if you find yourself using `as any` or `:any`, use the @think tool to think hard if there are types you can import instead. do even a search in the project for what the type could be. any should be used as a last resort.

- NEVER do `(x as any).field` or `'field' in x` before checking if the code compiles first without it. the code probably doesn't need any or the in check. even if it does not compile, use think tool first! before adding (x as any).something, ALWAYS read the .d.ts to understand the types

- after any change to typescript code ALWAYS run the `pnpm typecheck` script of that package, or if there is no typecheck script run `pnpm tsc` yourself

- do not declare uninitialized variables that are defined later in the flow. instead use an IIFE with returns. this way there is less state. also define the type of the variable before the iife. here is an example:

- use || over in: avoid 'x' in obj checks. prefer doing `obj?.x || ''` over doing `'x' in obj ? obj.x : ''`. only use the in operator if that field causes problems in typescript checks because typescript thinks the field is missing, as a last resort.

- when creating urls from a path and a base url, prefer using `new URL(path, baseUrl).toString()` instead of normal string interpolation. use type-safe react-router `href` or spiceflow `this.safePath` (available inside routes) if possible

- for node built-in imports, never import singular exported names. instead do `import fs from 'node:fs'`, same for path, os, etc.

- NEVER start the development server with pnpm dev yourself. there is no reason to do so, even with &

- When creating classes do not add setters and getters for a simple private field. instead make the field public directly so user can get it or set it himself without abstractions on top

- if you encounter typescript lint errors for an npm package, read the node_modules/package/\*.d.ts files to understand the typescript types of the package. if you cannot understand them, ask me to help you with it.

- NEVER silently suppress errors in catch {} blocks if they contain more than one function call
```ts
// BAD. DO NOT DO THIS
let favicon: string | undefined;
if (docsConfig?.favicon) {
  if (typeof docsConfig.favicon === "string") {
    favicon = docsConfig.favicon;
  } else if (docsConfig.favicon?.light) {
    // Use light favicon as default, could be enhanced with theme detection
    favicon = docsConfig.favicon.light;
  }
}
// DO THIS. use an iife. Immediately Invoked Function Expression
const favicon: string = (() => {
  if (!docsConfig?.favicon) {
    return "";
  }
  if (typeof docsConfig.favicon === "string") {
    return docsConfig.favicon;
  }
  if (docsConfig.favicon?.light) {
    // Use light favicon as default, could be enhanced with theme detection
    return docsConfig.favicon.light;
  }
  return "";
})();
// if you already know the type use it:
const favicon: string = () => {
  // ...
};
```

- when a package has to import files from another packages in the workspace never add a new tsconfig path, instead add that package as a workspace dependency using `pnpm i "package@workspace:*"`

NEVER use require. always esm imports

always try to use non-relative imports. each package has an absolute import with the package name, you can find it in the tsconfig.json paths section. for example, paths inside website can be imported from website. notice these paths also need to include the src directory.

this is preferable to other aliases like @/ because i can easily move the code from one package to another without changing the import paths. this way you can even move a file and import paths do not change much.

always specify the type when creating arrays, especially for empty arrays. if you don't, typescript will infer the type as `never[]`, which can cause type errors when adding elements later.

**Example:**

```ts
// BAD: Type will be never[]
const items = [];

// GOOD: Specify the expected type
const items: string[] = [];
const numbers: number[] = [];
const users: User[] = [];
```

remember to always add the explicit type to avoid unexpected type inference.

- when using nodejs APIs like fs always import the module and not the named exports. I prefer hacing nodejs APIs accessed on the module namspace like fs, os, path, etc.

DO `import fs from 'fs'; fs.writeFileSync(...)`
DO NOT `import { writeFileSync } from 'fs';`
