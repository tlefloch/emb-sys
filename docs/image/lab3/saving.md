# D. Cloning the image

To save all the changes made on the Raspberry PI image, we can clone the image from the sd-card

After stopping the RPI (unplug the power supply) , we remove the
sd-card and mount it on the computer

<!-- The sd-card may be larger than the Raspberry OS image (for example 32
Gb sd-card for our image of 4 Gb)

Use `lsblk` and look for something like `/dev/sd*`

We use the fdisk command to get the actual size of the image.  
Replace `?` in `/dev/sd?` with the right letter:
```bash
sudo fdisk -l /dev/sd?
``` -->

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