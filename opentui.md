
## opentui

opentui is the framework used to render the tui, using react.

IMPORTANT! before starting every task ALWAYS read opentui docs with `curl -s https://raw.githubusercontent.com/sst/opentui/refs/heads/main/packages/react/README.md`

ALWAYS!

## React

NEVER NEVER use forwardRef. it is not needed. instead just use a ref prop like React 19 best practice

NEVER pass function or callbacks as dependencies of useEffect, this will very easily cause infinite loops if you forget to use useCallback

NEVER use useCallback. it is useless if we never pass functions in useEffect dependencies

Try to never use useEffect if possible. usually you can move logic directly in event handlers instead


## understanding how to use opentui React elements

This is not a plain react project, instead it is a React with opentui renderer, which supports box, group, input, etc

To understand how to use these components read other files in the project. try to use the theme.tsx file for colors


## researching opentui patterns

you can read more examples of opentui react code using gitchamber by listing and reading files from the correct endpoint: https://gitchamber.com/repos/sst/opentui/main/files?glob=packages/react/examples/**

or for example to see how to use the `<code>` opentui element: https://gitchamber.com/repos/sst/opentui/main/search/<code?glob=\*\*

do something like this for every new element you want to use and not know about, for exampel `<scrollbox>`, to see examples

## keys

cdm modifier cannot be intercepted in opentui. because parent terminal app will not forward it. instead use alt or ctrl .

## overlapping text in boxes

if you see text elements too close to each other the issues is probably that the content does not fix in the box row so elements shrink and gaps or paddings are no longer respected. 

to fix this issue add flexShrink={0} to all elements inside the row
