# react

- never test react code. instead put as much code as possible in react-agnostic functions or classes and test those if needed.

- hooks, all functions that start with use, MUST ALWAYS be called in the component render scope, never inside other closures in the component or event handlers. follow react rules of hooks.

- always put all hooks at the start of component functions. put hooks that are bigger and longer later if possible. all other non-hooks logic should go after hooks section, things like conditionals, expressions, etc

## react code

- `useEffect` is bad: the use of useEffect is discouraged. please do not use it unless strictly necessary. before using useEffect call the @think tool to make sure that there are no other options. usually you can colocate code that runs inside useEffect to the functions that call that useEffect dependencies setState instead

- too many `useState` calls are bad. if some piece of state is dependent on other state just compute it as an expression in render. do not add new state unless strictly necessary. before adding a new useState to a component, use @think tool to think hard if you can instead: use expression with already existing local state, use expression with some global state, use expression with loader data, use expression with some other existing variable instead. for example if you need to show a popover when there is an error you should use the error as open state for the popover instead of adding new useState hook

- `useCallback` is bad. it should be always avoided.

- NEVER pass functions to useEffect or useMemo dependencies. when you start passing functions to hook dependencies you need to add useCallback everywhere in the code, useCallback is a virus that infects the codebase and should be ALWAYS avoided.

- custom hooks are bad. NEVER add custom hooks unless asked to do so by me. instead of creating hooks create generic react-independent functions. every time you find yourself creating a custom hook call @think and think hard if you can just create a normal function instead, or just inline the expression in the component if small enough

- minimize number of props. do not use props if you can use zustand state instead. the app has global zustand state that lets you get a piece of state down from the component tree by using something like `useStore(x => x.something)` or `useLoaderData<typeof loader>()` or even useRouteLoaderData if you are deep in the react component tree

- do not consider local state truthful when interacting with server. when interacting with the server with rpc or api calls never use state from the render function as input for the api call. this state can easily become stale or not get updated in the closure context. instead prefer using zustand `useStore.getState().stateValue`. notice that useLoaderData or useParams should be fine in this case.

- when using useRef with a generic type always add undefined in the call, for example `useRef<number>(undefined)`. this is required by the react types definitions

- when using && in jsx make sure that the result type is not of type number. in that case add Boolean() wrapper. this way jsx will not show zeros when the value is falsy.

## components

- place new components in the src/components folder. shadcn components will go to the src/components/ui folder, usually they are not manually updated but added with the shadcn cli (which is preferred to be run without npx, either with pnpm or globally just shadcn)

- component filenames should follow kebab case structure

- do not create a new component file if this new code will only be used in another component file. only create a component file if the component is used by multiple components or routes. colocate related components in the same file.

- non component code should be put in the src/lib folder.

- hooks should be put in the src/hooks.tsx file. do not create a new file for each new hook. also notice that you should never create custom hooks, only do it if asked for.

## zustand

zustand is the preferred way to created global React state. put it in files like state.ts or x-state.ts where x is something that describe a portion of app state in case of multiple global states or multiple apps

- minimize number of props. do not use props if you can use zustand state instead. the app has global zustand state that lets you get a piece of state down from the component tree by using something like `useStore(x => x.something)` or `useLoaderData<typeof loader>()` or even useRouteLoaderData if you are deep in the react component tree

- do not consider local state truthful when interacting with server. when interacting with the server with rpc or api calls never use state from the render function as input for the api call. this state can easily become stale or not get updated in the closure context. instead prefer using zustand `useStore.getState().stateValue`. notice that useLoaderData or useParams should be fine in this case.

- NEVER add zustand state setter methods. instead use useStore.setState to set state.

- zustand already merges new partial state with the previous state. NEVER DO `useStore.setState({ ...useStore.getInitialState(), ... })` unless for resetting state
