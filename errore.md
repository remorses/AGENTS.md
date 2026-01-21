## error handling in TypeScript

NEVER use try catch blocks. instead use errore package for type safe errors. always treat errors as values. returning them from functions.

always import errore when you need to use it for error handling as follows: `import * as errore from 'errore'`

if you need to use external functions we do not control wrap them with `errore.try(() => JSON.parse(something))` or `tryAsync`

before using errore in a session ALWAYS read the full errore readme with `curl -fs https://raw.githubusercontent.com/remorses/errore/refs/heads/main/README.md`
