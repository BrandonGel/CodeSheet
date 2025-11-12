# IsaacSim cheat sheet

Reading cpu power
```
cpupower frequency-info
```
Changing cpu power from powersave to performance
```
sudo apt update
sudo apt install linux-tools-common
sudo cpupower frequency-set --governor performance
```

Turn off Linux IOMMU
Locate ```GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"``` in  ```/etc/default/grub```

AMD
```GRUB_CMDLINE_LINUX_DEFAULT="quiet splash amd_iommu=off"```

Intel
```GRUB_CMDLINE_LINUX_DEFAULT="quiet splash intel_iommu=off"```

```
sudo update-grub
```
```
sudo reboot
```
