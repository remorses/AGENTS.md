# react router v7

the website uses react-router v7.

NEVER start the dev server yourself with `pnpm dev`, instead ask me to do so.

react-router framework is the successor of remix. it is basically the same framework and it uses loaders and actions as core features.

react-router follows all the conventions of remix but all imports must be updated to point to `react-router` instead of `@remix-run/react` or `@remix-run/node`.

## react-router navigation state

react-router has the hook `useNavigation` that exposes the navigation state. ALWAYS use this hook to track loading state for navigation

```ts
const navigation = useNavigation()

if (navigation.state === 'loading' || navigation.state === 'submitting') {
    return null
}
```

> when making changes to the website code only use the `pnpm typecheck` script to validate changes, NEVER run `pnpm build` unless asked. It is too slow.

## Creating New Routes and Handling Types

When creating a new React Router route, follow these steps:

### 1. Create the route file
Create a file in `src/routes/` using flat routes naming convention (dots for separators, $ for params, kebab-case).

### 2. Generate types
**IMPORTANT**: Types are NOT automatically generated. After creating a route, run:
```bash
pnpm exec react-router typegen
```

### 3. Import Route types
```typescript
import type { Route } from './+types/your-route-name'
```
Note: The `+types` directory doesn't physically exist - it's virtual/generated.

### 4. Verify with typecheck
```bash
pnpm typecheck  # This runs typegen first, then tsc
```

### Troubleshooting Missing Types
- Types missing? Run `pnpm exec react-router typegen`
- Import failing? Check filename matches import path exactly
- Types not updating? Run `pnpm typecheck` to regenerate
- The `+types` directory is virtual - don't look for it in the filesystem

### Best Practices
- Always run `pnpm typecheck` after creating/modifying routes
- Export `Route` type from layout routes for child routes to import
- Use `href()` for all internal paths, even in redirects

## react-router layout routes

react-router layout routes are simply routes that share a prefix with some children routes. these routes will run their loaders and components also when the children paths are fetched.

components can render children routes using the Outlet component

```tsx
export function Component() {
    return <Outlet />
}
```

the loader data from parent layouts will NOT be present in the children routes `Route.componentProps['loaderData']` type. instead you have to use the `useRouteLoaderData('/prefix-path')` instead. always add the type to these calls getting the `Route` type from the parent layout

> layout routes should ALWAYS export their own Route namespace types so that child route can use it to type `useRouteLoaderData`!

## cookies

never use react-router or remix `createCookieSessionStorage`. instead just use the npm cookie package to serialize and parse cookies. keep it simple.

if you want to store json data in cookies, remember to use encodeURIComponent to encode the data before storing it in the cookie, and decodeURIComponent to decode it when reading it back. this is because cookies can only store string values.

## website, react-routes

website routes use the flat routes filesystem routes, inside src/routes. these files encode the routing logic in the filename, using $id for params and dot . for slashes.

if 2 routes share the same prefix, then the loader of both routes is run on a request and the route with the shorter route name is called a layout. a layout can also use <Outlet /> to render the child route inside it. for example, /org/x/site will run loaders in `org.$orgid` and `org.$orgid.site`. if you want instead to create a route that is not a layout route, where the loader does not run for routes that share the prefix, append \_index to the filename, for example `org.$orgid._index` in the example before.

if you need to add new prisma queries or data fetching in loaders, put it in layouts if possible. this way the data is fetched less often. you can do this if the data does not depend on the children routes' specific parameters.

## route file exports

you can export the functions `loader` and `action` to handle loading data and submitting user data.

the default export (not always required for API routes) is the jsx component that renders the page visually.

notice that the `json` util was removed from `react-router`. instead there is a function `data` which is very similar and accepts a second argument to add headers and status like `json` does, but it supports more data types than json, like generators, async generators, dates, map, sets, etc.

## Route type safety

react-router exports a `Route` namespace with types like `Route.LoaderArgs`, `Route.ActionArgs` and `Route.ComponentProps`

these types can be used for the main route exports, they must be imported from `./+types/{route-basename}`

for example, if the current file is `src/routes/home.tsx` you can import `import { Route } from './+types/home'`.

when using loader data in components, it is preferable to use useRouteLoaderData instead of just useLoaderData, so that if the route data is not accessible an error is thrown instead of silently failing with the wrong data.

you can use the Route types even to type other components that rely on `useRouteLoaderData`. but to do this you cannot import from `+types`, only route files can do that. instead you should export the Route type from the route file and let the component file import from the route.

here is an example to get the loader data type safely from a component:

> useRouteLoaderData return type is `Route.componentProps['loaderData']`

```ts
import type { Route } from 'website/src/routes/root'

const { userId } = useRouteLoaderData(
    'root',
) as Route.componentProps['loaderData']
```

```ts
// this path should export Route first. make sure of that
import type { Route } from 'website/src/routes/org.$orgId'

const { userId } = useRouteLoaderData(
    'routes/org.$orgId',
) as Route.componentProps['loaderData']
```

you can do the same thing with action data, using `Route.componentProps['actionData']`

## links type safety

ALWAYS use the react-router href function to create links, it works as follow

```ts
import { href } from 'react-router'

const path = href('/org/:orgId', { orgId })
```

if you need to have an absolute url you can do `new URL(href('/some/path'), env.PUBLIC_URL)`

the only case where you should not use href is for urls outside of the current app or routes like `routes/$.tsx`, basically routes that match all paths.

> if you cannot use `href` simply because the route you would like to link to does not exist, you should do the following: list all the files in the src/routes folder first, to see if it already exists but not with the name you would expect. if still you can't find one, create a simple placeholder react-router route with a simple page component and a simple loader that does what you would expect. do not write too much code. you can improve on it in later messages.

## showing spinner while loader does work and then redirect

for routes that do slow operations like creating PRs and then redirect, use a loader that returns a promise. the component uses window.location.replace when the promise resolves.

> IMPORTANT: react router does not preserve errors thrown in promises returned from loaders. NEVER throw errors inside promises returned from loaders. instead, add a .catch to make sure errors are never thrown and returned as values instead. then use instanceof check in client

```tsx
export async function loader({ request, params: { id } }: Route.LoaderArgs) {
    const url = new URL(request.url)
    const data = url.searchParams.get('data')
    const promise = doSlowWork(id, data)
        .catch(error => {
            notifyError(error)
            return error
        })
    return { promise }
}

export default function Page() {
    const { promise } = useLoaderData<typeof loader>()
    const [error, setError] = useState('')

    useEffect(() => {
        promise.then(result => {
            if (result instanceof Error) {
                setError(result.message)
                return
            }
            window.location.replace(result.url)
        })
    }, [promise])

    if (error) return <p className='text-red-600'>Error: {error}</p>
    return <Loader2Icon className='h-6 w-6 animate-spin' />
}
```

## do not redirect to missing routes that do not exist

never redirect or link to a route that does not exist. instead create a simple placeholder route with a simple loader and component. then redirect there using type-safe path with `href`

if instead it's not clear where to redirect because a user resource is missing, check if an onboarding route exists for that resource or a generic onboarding route. redirect there instead

also keep in mind it's preferable to throw redirects in loaders instead of returning responses, so loader keeps type safety.

## client side navigation is preferred

always try to use react-router `useNavigate` or `Link` instead of doing window.location.href update.

so that internal navigation is done client side and is faster. notice that navigate only accepts a relative path and not a full url, so if you have a full url you should do new URL(url).pathname. only use navigate if you know the url is relative to the app.

## Link or a components are preferred over `navigate`

ALWAYS use link components instead of the navigate function if possible. for example, in a dropdown component you should wrap the dropdown item in a link instead of adding an onClick handler.

## debugging build failures

when you build the website always pipe the output to a file so you can later grep inside it for errors. with `pnpm build 2>&1 | build.log`
