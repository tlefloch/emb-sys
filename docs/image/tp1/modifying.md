# D. Modifying the Raspberry Pi OS image

!!! Note "1 - Issue"
    First mount the image's root file system as you have done before using `losetup` and `mount`

<!-- !!! Tip "1 - Solution"

    ```bash
    sudo losetup -P /dev/loop50 imgs/2024-11-19-raspios-bookworm-arm64-lite.img
    sudo mount /dev/loop50p2 /mnt/rpi
    ``` -->

Now the image is mounted, we can modify it (writing files, configuring parameters, etc).

!!! Note "2 - Issue"
    Change the name raspberrypi name with the name of your choice :

    - In the `/etc/hosts` file of the Raspberry Pi OS image, which is used to translate the host name into its ip address.  
    - In the `/etc/hostname` file of the Raspberry Pi OS image, which is used to define the hostname

!!! Warning
    Be careful not to modify your local OS' files, but those in `/mnt/rpi`

<!-- !!! Tip "2 - Solution"

    Change the name raspberry with the name you have chosen before :
    ```bash
    sudo nano /mnt/rpi/etc/hosts
    ```
    ```bash
    sudo nano /mnt/rpi/etc/hostname
    ``` -->

Clean end
```bash
sudo umount /mnt/rpi
sudo losetup -d /dev/loop50
```