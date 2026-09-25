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
