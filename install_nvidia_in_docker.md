# For MacOS
## If you are using Nvidia Driver
1. sudo apt-get install nvidia-384
2. use "softwater Updates" to switch to nouveau and apply
3. sudo reboot
4. Do Next Steps

## If you are using Nouveau Driver
Open “/etc/modprobe.d/blacklist.conf” file and add the following line. Save and close the file.
```
blacklist nouveau
```

## Next Steps
1. switch to tty1
2. sudo service lightdm stop
3. docker start xxxx
4. ./docker/script/dev_into.sh
5. sudo ./NVIDIA-Linux-x86_64-375.39.run --no-opengl-files -a -s  //install driver
6. exit container and install Nvidia driver(same command above) on Host
7. sudo reboot and enjoy!

## Docker gcc version issue
* Root cause: docker may use gcc-4.8, which cannot install NVIDIA driver(build kernel failed).
* Solution: apt-get intall gcc-4.9 in docker and change the softlink of gcc under /usr/bin to gcc-4.9, install driver and restore the softlink back to gcc-4.8
