When in Grub for ubuntu, look for the drive that has ubuntu. A good way to check is to perform ls to see the content of the hard drive and its partition. Ubuntu should have bin, boot, etc.
```
ls (hd0, gpt5)/
```
"hd0" represents hard drive 0. "gpt5" represents which partition in the hardrive.
```
ls (hd0, gpt5)
set prefix0=(hd0,gpt5)/boot/grub
insmod linux
insmod normal
normal
```
Now, you should see the default ubuntu and window screen. After logging into your linux user account, then enter the following commands in your terminal.
```
sudo grub-install
sudo update-grub
```
As added insurance, download Ubuntu boot-repair as it can fix the boot start.
```
sudo add-apt-repository ppa:yannubuntu/boot-repair && sudo apt update
sudo apt install -y boot-repair && boot-repair
```
