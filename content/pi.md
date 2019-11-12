---
title: "Raspberry Pi notes"
date: 2019-11-11T14:57:48+01:00
draft: false
---
This is my notes based on Raspberry Pi 3 running [Arch Linux](https://archlinuxarm.org/).


### Boot Arch Linux form USB on the Rasberry Pi 3
1. First enable boot from USB on your Raspberry Pi 3 -- [Official Guide](https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/msd.md)
2. Follow the official install guide from [Arch Linux ARM](https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-3) using a USB key instead of an SD card.
 Do not dismount after the basic install.
3. Find UUID of both partitions on your USB key -- you can use `lsblk -f`
4. Replace `/root=/dev/...` with `/root=UUID=<UUID of root partition>` in `cmdline.txt` on the mounted __boot__ partition.
5. Replace `/dev/mmcblk0p1` with `/dev/disk/by-uuid/<UUID of boot partition>` in `/etc/fstab` on the mounted __root__ partition.
6. Run `sync` and unmount partitions.
7. That's it -- just power off your Raspberry Pi, remove SD card, insert USB and power on :-)

NOTE: I use UUID to make sure it's the right partition if there is more than one USB key on the Raspberry Pi.

If you have an existing installation on an SD card you would like to move to USB, then just create the two partitions on the USB, mount them and copy the files using `rsync`.
~~~~
rsync -avhPHAXx /boot /mnt/boot
rsync -avhPHAXx --exclude={/dev/*,/proc/*,/sys/*,/tmp/*,/run/*,/mnt/*,/media/*,/lost+found} / /mnt/root
~~~~
Then follow the guide from step 3. You might have multiple entries in `/etc/fstab` -- convert them all to UUID.



### Get current config
~~~~
/opt/vc/bin/vcgencmd get_config int
/opt/vc/bin/vcgencmd get_config str
~~~~

### config.txt
~~~~
gpu_mem=16
~~~~

### Show current voltage
~~~~
for id in core sdram_c sdram_i sdram_p ; do echo -e "$id:\t$(/opt/vc/bin/vcgencmd measure_volts $id)" ; done
~~~~
