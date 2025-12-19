# D. Modifying the Raspberry Pi OS image

First mount the image
```bash
sudo losetup -P /dev/loop50 imgs/2024-11-19-raspios-bookworm-arm64-lite.img
sudo mount /dev/loop50p2 /mnt/rpi
```

Now the image is mounted, we can modify it (writing files, configuring parameters, etc).

For example:  
Change the name raspberry with the name of your choice
```bash
sudo nano /mnt/rpi/etc/hosts
```

Change the name raspberry with the name you have chosen before
```bash
sudo nano /mnt/rpi/etc/hostname
```

Clean end
```bash
sudo umount /mnt/rpi
sudo losetup -d /dev/loop50
```