# SSH

Connecting to remote machines (e.g. the lab servers), setting up keys so you don't type a password every time, copying files, forwarding ports, and the lab's server setup steps (Python environments, CUDA, picking a GPU).

Based on the lab's [Server Setup Guide](https://github.gatech.edu/pages/MCG-Lab/MCG-Lab.github.io/Server_Setup_Guide.html). The lab servers it names are `landru` and `spocks-brain`. Reserve time on the Server Schedule and ask the current server master which GPU you have.

> **Note:** the guide doesn't give the servers' full addresses, so `SERVER_HOST` below is a placeholder (e.g. `landru` plus its domain).

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
Georgia Tech servers are usually only reachable on the campus network. From anywhere else, connect to the GT VPN (GlobalProtect, portal `vpn.gatech.edu`) first. *(Not in the lab guide; general GT advice.)*

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
Optional, but you won't need to type passwords to log in, or to push and pull from GitHub. Make a key on **both** your own machine and the server (the server's key is for GitHub).

Create a key pair (press Enter to accept the default location; a passphrase is optional but recommended). The lab guide uses `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`; `ed25519` is the newer, shorter equivalent.

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

If that doesn't work, add it by hand: print your public key locally with `cat ~/.ssh/id_ed25519.pub`, copy the output, then on the server:
```bash
mkdir -p ~/.ssh && chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys   # paste the key on its own line, Ctrl+X then Y to save
chmod 600 ~/.ssh/authorized_keys
```

The same public key works for GitHub (github.com and github.gatech.edu). The lab guide recommends moving code between your machine and the server by cloning the repo on both and pushing/pulling, rather than copying files. See [git.md](git.md) for pointing a repo at the SSH remote.

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
Start Jupyter on the server (this is the lab guide's setup):
```bash
jupyter notebook --no-browser --port=8889
```

On your computer, forward your local port 8888 to the server's 8889, then open `http://localhost:8888` in your browser.
```bash
ssh -N -f -L localhost:8888:localhost:8889 lab
```

`-N` means "don't open a shell, just forward" and `-f` sends it to the background. For TensorBoard, use its port (usually `6006`) on both sides.

## Keeping jobs running after you disconnect
Anything started in a plain SSH session stops when the connection drops. Run long jobs inside `tmux`.

```bash
tmux new -s train      # start a named session
# run your job, then press Ctrl+B then D to detach
tmux ls                # list sessions
tmux attach -t train   # reattach later
```

## Lab server setup
Run these on the server after you log in.

### Python environments with virtualenvwrapper
Add these lines to the bottom of `~/.bashrc` (`nano ~/.bashrc`, then Ctrl+X, Y to save), then run `source ~/.bashrc`:
```bash
export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3
source /usr/local/bin/virtualenvwrapper.sh
export WORKON_HOME=~/.virtual_envs
```

Then:
```bash
mkvirtualenv my_env                              # create (and enter) an environment
mkvirtualenv -r requirements.txt my_env          # create it from a requirements file
workon my_env                                    # enter it later
deactivate                                       # leave it
```
`pip install` works inside it without `sudo`. Conda works too; see [condarepo.md](condarepo.md).

### Tell your account where CUDA is
Add to the bottom of `~/.bashrc`, then `source ~/.bashrc` (or log out and back in):
```bash
export PATH=/usr/local/cuda/bin:${PATH}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:${LD_LIBRARY_PATH}
```

### Use only your reserved GPU and a few CPUs
Prefix your run command. Without `OMP_NUM_THREADS`, PyTorch uses every CPU and slows everyone else down.
```bash
CUDA_VISIBLE_DEVICES=0 OMP_NUM_THREADS=4 python train.py
```

**On `spocks-brain` the numbers don't match `nvidia-smi`.** If you were given GPU `y` (as `nvidia-smi` shows it), use `CUDA_VISIBLE_DEVICES=(y+3) mod 5`:

| `nvidia-smi` GPU | `CUDA_VISIBLE_DEVICES` |
|---|---|
| 0 | 3 |
| 1 | 4 |
| 2 | 0 |
| 3 | 1 |
| 4 | 2 |

TensorFlow grabs all GPU memory by default. To share a GPU between several runs, enable memory growth (TF 2.x):
```python
import tensorflow as tf
for gpu in tf.config.list_physical_devices("GPU"):
    tf.config.experimental.set_memory_growth(gpu, True)
```
*(The lab guide shows the older TF 1.x `ConfigProto` version with `per_process_gpu_memory_fraction` / `allow_growth`.)*

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
