# Vercel Sandbox

use `sandbox` CLI to run commands in ephemeral remote Linux VMs. useful for isolated code execution, testing untrusted code, or running agent-generated scripts safely.

## running commands

sandbox runs commands non-interactively (no TTY needed):

```bash
sandbox run echo "hello world"                    # one-shot command
sandbox run node -e "console.log(2+2)"            # run node code
sandbox run python -c "print('hello')"            # run python (use --runtime python3.13)
sandbox run --rm ls -la                           # auto-cleanup after command
```

for multiple commands, manage sandbox lifecycle manually:

```bash
ID=$(sandbox create -q)                           # create sandbox, get ID
sandbox exec $ID node -e "console.log('step 1')"
sandbox exec $ID node -e "console.log('step 2')"
sandbox stop $ID                                  # cleanup
```

## installing packages

```bash
sandbox run --sudo dnf install -y golang          # system packages (Amazon Linux)
sandbox run npm install express                   # npm packages
sandbox run pip install requests                  # python packages (with --runtime python3.13)
```

available runtimes: `node22` (default), `python3.13`

## file paths

sandbox has an isolated filesystem. the writable directory is `/vercel/sandbox`:

```bash
sandbox exec $ID touch /vercel/sandbox/foo.txt   # works
sandbox exec $ID touch /home/test.txt            # permission denied
```

## copying files

use `sandbox cp` to transfer files between local and remote:

```bash
# copy local file to sandbox
sandbox cp ./script.js $ID:/vercel/sandbox/

# copy from sandbox to local
sandbox cp $ID:/vercel/sandbox/output.txt ./

# copy directory
sandbox cp -r ./src $ID:/vercel/sandbox/
```

## reading logs

command output is returned directly from `sandbox exec` and `sandbox run`:

```bash
# stdout is printed directly
sandbox run node -e "console.log('hello')"
# Output: hello

# capture output in a variable
OUTPUT=$(sandbox exec $ID node -e "console.log(JSON.stringify({ok:true}))")
echo $OUTPUT

# stderr is also printed
sandbox run node -e "console.error('warning')"
```

for long-running processes or debugging:

```bash
# write logs to file, then retrieve
sandbox exec $ID bash -c "node app.js > /vercel/sandbox/app.log 2>&1"
sandbox cp $ID:/vercel/sandbox/app.log ./

# or tail logs in real-time (requires -t for streaming)
sandbox exec -t $ID tail -f /vercel/sandbox/app.log
```
