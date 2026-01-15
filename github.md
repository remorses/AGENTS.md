# github


you can use the `gh` cli to do operations on github for the current repository. For example: open issues, open PRs, check actions status, read workflow logs, etc.

## creating issues and pull requests

when opening issues and pull requests with gh cli, never use markdown headings or sections. instead just use simple paragraphs, lists and code examples. be as short as possible while remaining clear and using good English.

example:

```bash
gh issue create --title "Fix login timeout" --body "The login form times out after 5 seconds on slow connections. This affects users on mobile networks.

Steps to reproduce:
1. Open login page on 3G connection
2. Enter credentials
3. Click submit

Expected: Login completes within 30 seconds
Actual: Request times out after 5 seconds

Error in console:
\`\`\`bash
Error: Request timeout at /api/auth/login
\`\`\`"
```

## get current github repo

`git config --get remote.origin.url`

## checking status of latest github actions workflow run

```bash
gh run list # lists latest actions runs
gh run watch <id> --exit-status # if workflow is in progress, wait for the run to complete. the actions run is finished when this command exits. Set a tiemout of at least 10 minutes when running this command
gh pr checks --watch --fail-fast # watch for current branch pr ci checks to finish
gh run view <id> --log-failed | tail -n 300 # read the logs for failed steps in the actions run
gh run view <id> --log | tail -n 300 # read all logs for a github actions run
```

## responding to PR reviews and comments (gh-pr-review extension)

```bash
# view reviews and get thread IDs
gh pr-review review view 42 -R owner/repo --unresolved

# reply to a review comment
gh pr-review comments reply 42 -R owner/repo \
  --thread-id PRRT_kwDOAAABbcdEFG12 \
  --body "Fixed in latest commit"

# resolve a thread
gh pr-review threads resolve 42 -R owner/repo --thread-id PRRT_kwDOAAABbcdEFG12
```

## reading github repos source code

```sh
opensrc zod # npm package name

# Using github: prefix
opensrc github:owner/repo

# Using owner/repo shorthand
opensrc facebook/react

# Using full GitHub URL
opensrc https://github.com/colinhacks/zod

# Fetch a specific branch or tag
opensrc owner/repo@v1.0.0
opensrc owner/repo#main

# Mix packages and repos
```

This will download the source code in ./opensrc. which should be put in .gitignore
