# C. Increasing size of Raspberry Pi OS image

Suppose that we are in the rpilab folder, go to imgs subfolder :

```bash
cd imgs
```

Extend partition 2 (rootfs) with 2 GB (choose one of the two methods)

!!! Note "method 1"

    ```bash
    truncate --size=+2G 2024-11-19-raspios-bookworm-arm64-lite.img
    ```

!!! Note "method 2"

    ```bash
    dd if=/dev/zero bs=1M count=2048 >> 2024-11-19-raspios-bookworm-arm64-lite.img
    ```

    Check that partition 2 has not been resized yet
    ```bash
    sudo fdisk -l 2024-11-19-raspios-bookworm-arm64-lite.img
    ```

    Then, use `parted` to actually extend the partition 2
    ```bash
    sudo parted 2024-11-19-raspios-bookworm-arm64-lite.img
    ```

    On the command prompt,type:
    ```bash
    p
    resizepart 2 -1
    p
    q
    ```

Mount the image in a loop device (we use /dev/loop50 as before)
```bash
sudo losetup -P /dev/loop50 2024-11-19-raspios-bookworm-arm64-lite.img
```

Check file system and extend file system to fully fill partition 2
```bash
sudo e2fsck -f /dev/loop50p2
sudo resize2fs /dev/loop50p2
```

Detach cleanly

```bash
sudo losetup -d /dev/loop50
```

back to rpilab folder
```bash
cd ..
```