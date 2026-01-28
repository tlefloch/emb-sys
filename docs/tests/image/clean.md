# J. Clean chroot

Clean your chroot setup :

```bash
sudo rm /mnt/rpi/etc/resolv.conf
sudo umount /mnt/rpi/{dev/pts,dev,sys,proc,boot,}
sudo umount -f /mnt/rpi
sudo losetup -d <your_loop_device>
```

!!! Tip
    If you can't unmount /mnt/rpi because device is busy, use `sudo umount -l /mnt/rpi`
