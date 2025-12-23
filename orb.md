# OrbStack Linux VM

use `orb` to run commands in a persistent linux VM. useful for linux-only debugging or isolated experiments.

## running commands

orb automatically uses your current mac directory and can access mac files directly:

```bash
orb uname -a              # run command in default machine
orb ls                    # lists files in current mac directory
orb ./script.sh           # run a script from current directory
orb                       # open interactive shell
```

## installing packages

```bash
orb sudo apt-get update
orb sudo apt-get install -y nodejs npm
orb node --version
```

## file paths

mac directories are a shared filesystem (not synced). this means:

- changes are instant in both directions, no delay
- files created in orb (in mac paths) appear immediately on mac
- files created on mac appear immediately in orb
- it's the same file, not a copy

```bash
orb touch foo.txt   # appears immediately on mac
touch bar.txt       # appears immediately in orb
orb cat bar.txt     # can read mac-created file instantly
```

path mapping:

- mac paths like `/Users/...` work directly in orb commands (auto-translated)
- from inside linux shell, mac files are also accessible at `/mnt/mac/...`
- files in `/home/morse/` are accessible from mac at `~/OrbStack/<machine>/home/morse/...`
- `/tmp/` is a tmpfs (RAM) - isolated from mac, lost on reboot

## avoiding file pollution

NEVER run commands in orb that create files in the current mac directory. since the filesystem is shared, these files will pollute your mac project:

- node_modules (linux binaries)
- build outputs (dist/, .next/, etc)
- downloads
- caches

instead, copy the project to `/tmp` and work there:

```bash
orb cp -r . /tmp/linux-vm-myproject
orb bash -c 'cd /tmp/linux-vm-myproject && npm install'
orb bash -c 'cd /tmp/linux-vm-myproject && npm run build'
orb bash -c 'cd /tmp/linux-vm-myproject && npm test'
```

`/tmp` is a tmpfs (RAM filesystem) - fully isolated from mac but files are lost on VM reboot.
