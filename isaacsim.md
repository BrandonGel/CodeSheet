# Isaac Sim cheat sheet

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
