When in Grub for ubuntu, look for the drive that has ubuntu. A good way to check is to perform ls to see the content of the hard drive and its partition. Ubuntu should have bin, boot, etc.
'''
ls (hd0, gpt5)/
'''
"hd0" represents hard drive 0. "gpt5" represents which partition in the hardrive.
'''
ls (hd0, gpt5)
set prefix0=(hd0,gpt5)/boot/grub
insmod normal
normal
'''
