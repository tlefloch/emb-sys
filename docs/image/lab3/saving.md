# D. Cloning the image

To save all the changes made on the Raspberry PI image, we can clone the image from the sd-card

After stopping the RPI (unplug the power supply) , we remove the
sd-card and mount it on the computer

## With dd

The sd-card may be larger than the Raspberry OS image (for example 32
Gb sd-card for our image of 4 Gb). We use the fdisk command to get
the actual size of the image. Replace ? in /dev/sd? with the right letter:
sudo fdisk -l /dev/sd?
The number of blocks is given is the ”End” value for ”/dev/sd?2” (ex
8724479). We use this count in the dd command to copy back the
modifed Raspberry OS image on our computer.

copy the image from the sd-card to the computer :
replace ? with the appropriate drive letter

!!! Warning
   When using the `dd` command make sure you don't overwrite the wrong partitions or you will get big trouble !

```bash
sudo dd if=/dev/sdb of=imgs/2024-11-19-raspios-bookworm-arm64-lite-modified-2024.img count=8724479 bs=512
```

!!! Note
    if the partition /dev/sd?2 is very large with lot of empty space,
    gparted command can be used to shrink it before doing dd (sudo gparted
    /dev/sd?2)


## With Balena Etcher

On your host machine :

1. Open Balena Etcher
2. Click “Clone drive”
3. Select your SD card
4. Select “Image file” as the destination
5. Choose filename (e.g. sdcard_backup.img)
6. Click Clone

!!! Note
    You could also use the `dd` command instead of Etcher but make sure you don't overwrite the wrong partitions or you will get big trouble !

**Congrats ! You ended this lab series !**  

You will soon use your knowledge to configure an embedded Linux for a robot.