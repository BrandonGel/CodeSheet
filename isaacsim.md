# Isaac Sim cheat sheet

> **Draft:** lines marked **(check)** are guesses I haven't confirmed against your setup. Delete each mark once it's verified.

## Set your version first
Type the Isaac Sim version once. The commands below use it, so you can copy and paste them as-is. Run this again in any new terminal.
```bash
export ISAACSIM_VERSION=6.2.0
```

## Check your machine
Ubuntu 22.04 or 24.04 with an NVIDIA RTX GPU **(check: requirements for 6.2.0)**. See which driver you have:
```bash
nvidia-smi
```

## Install with pip (in a Conda environment)
**(check)** the Python version for 6.2.0. Isaac Sim 5.x needed Python 3.11.
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
The standalone download also ships a checker: `./isaac-sim.compatibility_check.sh`.

## Build from the Git repo
Needs Git LFS and a C++ compiler **(check: GCC version for 6.2.0)**:
```bash
sudo apt update
sudo apt install git git-lfs build-essential
git lfs install
```
Clone the source and check out your version. **(check)** the tag name format.
```bash
cd ~
git clone https://github.com/isaac-sim/IsaacSim.git isaacsim-src
cd ~/isaacsim-src
git checkout "v$ISAACSIM_VERSION"
git lfs pull
./build.sh
```
The first build downloads several GB and takes a while. Launch it:
```bash
cd ~/isaacsim-src/_build/linux-x86_64/release
./isaac-sim.sh
```
Update to a new version later:
```bash
cd ~/isaacsim-src
git fetch --tags
git checkout "v$ISAACSIM_VERSION"
git lfs pull
./build.sh
```

## First launch
The first launch asks you to accept the EULA and takes several minutes while it builds the shader cache. To skip the EULA prompt:
```bash
export OMNI_KIT_ACCEPT_EULA=YES
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
