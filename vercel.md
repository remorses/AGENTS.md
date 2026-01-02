---
name: vercel
description: List deployments, check build status, and stream runtime logs with Vercel CLI
tags: [vercel, deployment, logs, cli]
---

# vercel

use the vercel cli to list deployments, check build status, and read runtime logs.

## listing deployments

```bash
vercel list                    # list recent deployments
vercel list --prod             # list only production deployments
vercel list --limit 5          # limit results
```

## deployment status and build logs

```bash
vercel inspect <deployment-url-or-id>                  # get deployment info and status
vercel inspect <deployment-url-or-id> --logs           # print build logs
vercel inspect <deployment-url-or-id> --logs --wait    # wait for build to complete, stream logs
vercel inspect <deployment-url-or-id> --wait --timeout=5m  # wait with timeout
```

## reading runtime logs

runtime logs only stream new logs from when you start the command. there is no way to fetch historical logs via cli.

```bash
vercel logs <deployment-url-or-id>          # stream logs for 5 minutes
vercel logs <deployment-url-or-id> --json   # output as json (useful for filtering)
```

> the `--since`, `--limit`, and `--follow` options are deprecated and ignored. use the vercel dashboard for historical logs.

## reading logs for latest deployment

get the latest production deployment url and stream its logs:

```bash
DEPLOY_URL=$(vercel list --prod --limit 1 | tail -n 1 | awk '{print $1}')
vercel logs "$DEPLOY_URL"
```

for preview deployments:

```bash
DEPLOY_URL=$(vercel list --limit 1 | tail -n 1 | awk '{print $1}')
vercel logs "$DEPLOY_URL"
```

## background log streaming with tmux

since `vercel logs` streams indefinitely (up to 5 minutes), use tmux to run it in background while you trigger errors. see tmux.md for more details.

1. get latest deployment url:

```bash
DEPLOY_URL=$(vercel list --prod --limit 1 | tail -n 1 | awk '{print $1}')
```

2. start log streaming in background:

```bash
tmux new-session -d -s vercel-logs-prod "vercel logs $DEPLOY_URL --json"
```

3. trigger an error (e.g. hit an endpoint that fails):

```bash
curl -s "https://$DEPLOY_URL/api/some-endpoint" || true
```

4. read captured logs:

```bash
tmux capture-pane -t vercel-logs-prod:0 -S -100 -p
```

5. filter for errors with jq:

```bash
tmux capture-pane -t vercel-logs-prod:0 -S -200 -p | jq -s 'map(select(.level == "error"))'
```

6. kill the session when done:

```bash
tmux kill-session -t vercel-logs-prod
```

## timeout wrapper

if you need logs for a fixed duration without tmux:

```bash
timeout 30s vercel logs <deployment-url> --json > logs.json
```

## limitations

- runtime logs stream only, no historical fetch via cli
- logs retained 1 hour (cli) or 3 days (dashboard)
- build logs stored indefinitely (truncated at 4MB)
- for long-term storage use log drains (pro/enterprise)
