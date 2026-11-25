# Ubuntu cheat sheet

## Recovering from the GRUB rescue prompt
List the drives and partitions GRUB can see:
```
ls
```
Look for the partition that has Ubuntu on it. Listing its contents should show `bin`, `boot`, `etc`, and so on. `hd0` is hard drive 0 and `gpt5` is partition 5 on it (no space after the comma):
```
ls (hd0,gpt5)/
```
Once you've found it, boot from it:
```
set root=(hd0,gpt5)
set prefix=(hd0,gpt5)/boot/grub
insmod linux
insmod normal
normal
```
You should now see the usual Ubuntu/Windows boot menu. Log into Ubuntu and reinstall GRUB so the fix sticks:
```bash
sudo grub-install
sudo update-grub
```

As extra insurance, install Boot-Repair, which can fix the boot setup automatically:
```bash
sudo add-apt-repository ppa:yannubuntu/boot-repair && sudo apt update
sudo apt install -y boot-repair && boot-repair
```

## Packages
Refresh the package list and upgrade everything:
```bash
sudo apt update && sudo apt upgrade -y
```
Install, remove, or fully remove (including config files) a package `PACKAGE`:
```bash
sudo apt install PACKAGE
sudo apt remove PACKAGE
sudo apt purge PACKAGE
```
Clean up dependencies nothing uses anymore and the downloaded package cache:
```bash
sudo apt autoremove -y && sudo apt clean
```
Search for a package, or check whether one is installed and which version:
```bash
apt search KEYWORD
apt list --installed | grep PACKAGE
```
Install a downloaded `.deb` file (pulls in its dependencies too):
```bash
sudo apt install ./FILE.deb
```
If an install was interrupted and apt complains about broken packages:
```bash
sudo dpkg --configure -a
sudo apt --fix-broken install
```

## Disk space
Free space on each mounted drive:
```bash
df -h
```
Size of each folder in the current directory, largest last:
```bash
du -sh * | sort -h
```
For an interactive view you can browse, install `ncdu` and run it on a folder:
```bash
sudo apt install ncdu
ncdu ~
```
List drives and partitions with their mount points:
```bash
lsblk
```
Shrink the system logs if `/var/log/journal` is taking a lot of space:
```bash
sudo journalctl --vacuum-size=500M
```

## Processes
Live view of CPU and memory use (`q` to quit). `htop` is easier to read but needs installing:
```bash
top
sudo apt install htop && htop
```
Find a process by name and get its PID:
```bash
pgrep -a NAME
```
Stop a process by PID, or by name. Add `-9` to force it if it won't stop:
```bash
kill PID
pkill NAME
kill -9 PID
```
See what is using a port `PORT`:
```bash
sudo lsof -i :PORT
```
Keep a command running after you close the terminal, with its output saved to `output.log`:
```bash
nohup COMMAND > output.log 2>&1 &
```

## NVIDIA drivers
Check the driver version and what is running on the GPU (watch it live with `watch`):
```bash
nvidia-smi
watch -n 1 nvidia-smi
```
List the drivers Ubuntu recommends for your GPU, then install the recommended one or a specific version `VERSION`:
```bash
ubuntu-drivers devices
sudo ubuntu-drivers install
sudo apt install nvidia-driver-VERSION
sudo reboot
```
If the driver breaks (black screen, `nvidia-smi` fails), remove it and reinstall:
```bash
sudo apt purge '^nvidia-.*'
sudo apt autoremove -y
sudo ubuntu-drivers install
sudo reboot
```

## System info
Ubuntu version, kernel version, and CPU/memory:
```bash
lsb_release -a
uname -r
lscpu
free -h
```
