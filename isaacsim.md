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
