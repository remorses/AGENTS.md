# styling

- always use tailwind for styling. prefer using simple styles using flex and gap. margins should be avoided, instead use flexbox gaps, grid gaps, or separate spacing divs.

- use shadcn theme colors instead of tailwind default colors. this way there is no need to add `dark:` variants most of the time.

- `flex flex-col gap-3` is preferred over `space-y-3`. same for the x direction.

- try to keep styles as simple as possible, for breakpoints too.

- to join many classes together use the `cn('class-1', 'class-2')` utility instead of `${}` or other methods. this utility is usually used in shadcn-compatible projects and mine is exported from `website/src/lib/cn` usually. prefer doing `cn(bool && 'class')` instead of `cn(bool ? 'class' : '')`

- prefer `size-4` over `w-4 h-4`

## components

this project uses shadcn components placed in the website/src/components/ui folder. never add a new shadcn component yourself by writing code. instead use the shadcn cli installed locally.

try to reuse these available components when you can, for example for buttons, tooltips, scroll areas, etc.

## reusing shadcn components

when creating a new React component or adding jsx before creating your own buttons or other elements first check the files inside `src/components/ui` and `src/components` to see what is already available. So you can reuse things like Button and Tooltip components instead of creating your own.
