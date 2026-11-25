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
The build needs Git LFS and a C++ compiler **(check: GCC version for 6.2.0)**:
```bash
sudo apt update
sudo apt install git git-lfs build-essential
git lfs install
```

## Install from the Git repo
Clone the source and check out your version. **(check)** the tag name format.
```bash
cd ~
git clone https://github.com/isaac-sim/IsaacSim.git isaacsim
cd ~/isaacsim
git checkout "v$ISAACSIM_VERSION"
git lfs pull
./build.sh
```
The first build downloads several GB and takes a while.

## Launching Isaac Sim
```bash
cd ~/isaacsim/_build/linux-x86_64/release
./isaac-sim.sh
```
The first launch asks you to accept the EULA and takes several minutes while it builds the shader cache. To skip the EULA prompt:
```bash
export OMNI_KIT_ACCEPT_EULA=YES
```
Run your own Python script with Isaac Sim's bundled Python:
```bash
cd ~/isaacsim/_build/linux-x86_64/release
./python.sh path/to/script.py
```

## Updating to a new version
```bash
export ISAACSIM_VERSION=6.2.0   # change to the new version
cd ~/isaacsim
git fetch --tags
git checkout "v$ISAACSIM_VERSION"
git lfs pull
./build.sh
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
