
## opentui

opentui is the framework used to render the tui, using react.

IMPORTANT! before starting every task ALWAYS read opentui docs with `curl -s https://raw.githubusercontent.com/sst/opentui/refs/heads/main/packages/react/README.md`

do this every time you have to edit .tsx files in the project.

## React

NEVER NEVER use forwardRef. it is not needed. instead just use a ref prop like React 19 best practice

NEVER pass function or callbacks as dependencies of useEffect, this will very easily cause infinite loops if you forget to use useCallback

NEVER use useCallback other than for ref callbacks. it is useless if we never pass functions in useEffect dependencies

Try to never use useEffect if possible. usually you can move logic directly in event handlers instead

This is not a plain react project, instead it is a project using opentui renderer, which supports box, group, textarea, etc

Styles are implemented via Yoga. there is a style prop to pass an object or you can also pass styles using a prop for each style (which is preferred)

Not all CSS and react style props are implemented. Only flexbox one. 

To understand how to use these components read other files in the project. try to use the theme.tsx file for colors.

## text wrapping

text elements wrap by default. to disable this pass wrapMode="none"


## researching opentui patterns

you can read more examples of opentui react code using gitchamber by listing and reading files from the correct endpoint: https://gitchamber.com/repos/sst/opentui/main/files?glob=packages/react/examples/**

or for example to see how to use the `<code>` opentui element: https://gitchamber.com/repos/sst/opentui/main/search/<code?glob=\*\*

do something like this for every new element you want to use and not know about, for exampel `<scrollbox>`, to see examples

## keys

cdm modifier (named hyper in opentui) cannot be intercepted in opentui. because parent terminal app will not forward it. instead use alt or ctrl

enter key is named return in opentui. alt is option.

## overlapping text in boxes

if you see text elements too close to each other the issues is probably that the content does not fit in the box row so elements shrink and gaps or paddings are no longer respected. 

to fix this issue add flexShrink={0} to all elements inside the row

this common when using wrapMode none.
