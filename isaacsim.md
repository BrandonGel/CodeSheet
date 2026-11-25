# Isaac Sim cheat sheet

> **Draft:** lines marked **(check)** are guesses I haven't confirmed against your setup. Delete each mark once it's verified.

## Set your version first
Type the Isaac Sim version once. The install commands below use it, so you can copy and paste them as-is. Run this again in any new terminal.
```bash
export ISAACSIM_VERSION=5.1.0
```
**(check)** 5.1.0 was the latest release I knew of. Isaac Sim 5.x needs Python 3.11; 4.x needs Python 3.10.

## Check your machine
Ubuntu 22.04 or 24.04 with an NVIDIA RTX GPU **(check)**. See which driver you have:
```bash
nvidia-smi
```
The standalone download also ships a checker (see below): `./isaac-sim.compatibility_check.sh`.

## Install with pip (in a Conda environment)
```bash
conda create -n isaacsim python=3.11 -y
conda activate isaacsim
pip install --upgrade pip
pip install "isaacsim[all,extscache]==$ISAACSIM_VERSION" --extra-index-url https://pypi.nvidia.com
```
Launch it:
```bash
conda activate isaacsim
isaacsim
```
The first launch asks you to accept the EULA and takes several minutes while it builds the shader cache. To skip the EULA prompt:
```bash
export OMNI_KIT_ACCEPT_EULA=YES
```

## Install from the standalone download
Download the Linux zip from the [Isaac Sim download page](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/download.html) into `~/Downloads`. **(check)** the zip name below.
```bash
mkdir -p ~/isaacsim
cd ~/Downloads
unzip "isaac-sim-standalone-${ISAACSIM_VERSION}-linux-x86_64.zip" -d ~/isaacsim
cd ~/isaacsim
./post_install.sh
```
Launch it:
```bash
cd ~/isaacsim
./isaac-sim.selector.sh   # opens a small picker for the app and options
./isaac-sim.sh            # or start the full app directly
```
Run your own Python script with Isaac Sim's bundled Python:
```bash
cd ~/isaacsim
./python.sh path/to/script.py
```

## CPU power mode
Check the current CPU frequency governor:
```bash
cpupower frequency-info
```
Switch from `powersave` to `performance`:
```bash
sudo apt update
sudo apt install linux-tools-common linux-tools-$(uname -r)
sudo cpupower frequency-set --governor performance
```

## Turning off IOMMU
Open `/etc/default/grub` and find the line:
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```
Change it depending on your CPU.

AMD:
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash amd_iommu=off"
```
Intel:
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash intel_iommu=off"
```
Apply the change and reboot:
```bash
sudo update-grub
sudo reboot
```
