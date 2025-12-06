
Use tmux to run long-lived background commands as background “tasks” like vite dev servers, commands with watch mode.  
Each task should be a tmux **session** that the agent can start, inspect, and stop via CLI.

ALWAYS give long and descriptive names for the sessions, so other agents know what they are for.

Run a background task (e.g. Vite dev server) without blocking:

```bash
tmux new-session -d -s project-name-vite-dev-port-8034 'cd /path/to/project && npm run dev --port 8034'
```

Every time you are about to start a new session, first check if there is one already.

List all background tasks (sessions):

```bash
tmux ls
```

Kill a background task:

```bash
tmux kill-session -t vite-dev
```

Never attach to a session. You are inside a non TTY terminal, meaning you instead will have to read the latest n logs instead.


Fetch the last N log lines for a task without attaching (returns immediately):

```bash
tmux capture-pane -t vite-dev:0 -S -100 -p
```

Example pattern for a coding agent:

1. Start a task:

   ```bash
   tmux new-session -d -s build 'cd /repo && npm run build'
   ```

2. Poll logs:

   ```bash
   tmux capture-pane -t build:0 -S -80 -p
   ```

3. List all running tasks:

   ```bash
   tmux ls
   ```

4. Stop a task when done:

   ```bash
   tmux kill-session -t build
   ```

