# SSH

Connecting to remote machines (e.g. a lab server), setting up keys so you don't type a password every time, copying files, and forwarding ports.

> **Draft note:** lab-specific values below (`SERVER_HOST`, the VPN step) are placeholders. Fill them in from the lab's server setup guide.

## Set your server details first
Type your username and the server's address once. Every command below uses them, so you can copy and paste them as-is. Run this again in any new terminal.

Linux / macOS:
```bash
export SSH_USER=gt_username
export SSH_HOST=SERVER_HOST
```

Windows (PowerShell):
```powershell
$env:SSH_USER = "gt_username"
$env:SSH_HOST = "SERVER_HOST"
```

## Before connecting from off campus
Georgia Tech servers are usually only reachable on the campus network. From anywhere else, connect to the GT VPN (GlobalProtect, portal `vpn.gatech.edu`) first. *(Guess: check the lab guide for whether the server needs the VPN.)*

## Connecting
Linux / macOS:
```bash
ssh "$SSH_USER@$SSH_HOST"
```

Windows (PowerShell, OpenSSH is built in on Windows 10+):
```powershell
ssh "$env:SSH_USER@$env:SSH_HOST"
```

Type `exit` (or press `Ctrl+D`) to disconnect.

## Logging in with a key instead of a password
Create a key pair (press Enter to accept the default location; a passphrase is optional but recommended).

Linux / macOS:
```bash
ssh-keygen -t ed25519 -C "$SSH_USER"
```

Windows (PowerShell):
```powershell
ssh-keygen -t ed25519 -C "$env:SSH_USER"
```

Copy the public key to the server. You'll type your password one last time.

Linux / macOS:
```bash
ssh-copy-id "$SSH_USER@$SSH_HOST"
```

Windows (PowerShell, there is no `ssh-copy-id`):
```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh "$env:SSH_USER@$env:SSH_HOST" "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

The same public key works for GitHub. See [git.md](git.md) for pointing a repo at the SSH remote.

## Short names with an SSH config file
Add an entry to `~/.ssh/config` (Windows: `C:\Users\YOU\.ssh\config`) so you can type `ssh lab` instead of the full address.

```
Host lab
    HostName SERVER_HOST
    User gt_username
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
```

Then:
```bash
ssh lab
```

`ServerAliveInterval` keeps idle connections from dropping. Every command below also works with `lab` in place of `user@host`.

## Copying files
`scp` for one-off copies, `rsync` (Linux/macOS) for folders you copy repeatedly, since it only sends what changed.

Local to server:
```bash
scp myfile.txt lab:~/
scp -r myfolder lab:~/
```

Server to local:
```bash
scp lab:~/results.csv .
scp -r lab:~/outputs .
```

Sync a folder (Linux / macOS):
```bash
rsync -avz --progress myfolder/ lab:~/myfolder/
```

## Port forwarding (Jupyter, TensorBoard)
Run the service on the server, then forward its port to your machine and open `http://localhost:8888` in your browser.

```bash
ssh -N -L 8888:localhost:8888 lab
```

`-N` means "don't open a shell, just forward". Change both numbers for other ports (TensorBoard is usually `6006`). On the server, start Jupyter with:
```bash
jupyter lab --no-browser --port 8888
```

## Keeping jobs running after you disconnect
Anything started in a plain SSH session stops when the connection drops. Run long jobs inside `tmux`.

```bash
tmux new -s train      # start a named session
# run your job, then press Ctrl+B then D to detach
tmux ls                # list sessions
tmux attach -t train   # reattach later
```

## Editing on the server from VS Code
Install the **Remote - SSH** extension, press `F1`, choose **Remote-SSH: Connect to Host...**, and pick `lab` (it reads your `~/.ssh/config`).

## Troubleshooting
- **`Connection timed out`**: you're probably off campus without the VPN, or the hostname is wrong.
- **`Permission denied (publickey)`**: the server doesn't have your public key. Redo the `ssh-copy-id` step, or check `IdentityFile` in your config.
- **`WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED`**: the server was reinstalled (or something is wrong). If you trust it, remove the old entry:
  ```bash
  ssh-keygen -R SERVER_HOST
  ```
- **Debug output**: add `-v` to see where the connection fails.
  ```bash
  ssh -v lab
  ```
