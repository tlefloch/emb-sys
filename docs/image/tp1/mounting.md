# B. Mounting the Raspberry Pi OS image on the host

The Raspberry Pi OS image has 2 partitions : rootfs and boot. We will
now mount these partitions on the host to get access to the files and
folders in the image.

The boot partition contains bootloader information (used at start).  
The rootfs partition contains the file system of raspios bookworm.

!!! Tip "Definition"
    Mounting a partition is the act of making the filesystem on that partition accessible at a specific location in the directory tree so the operating system can read from and write to it.

We will now mount the rootfs partition on our computer to have access
to the file system of raspios bookworm.

!!! Note
    We will go throuhg 2 different methods.  
    The first one will teach us more how the image is structured.  
    The second one is more efficient and recommended.


## First method (Intuitive)


!!! Note "1 - Issue"
    Use the `fdisk` command with the correct option to display a list of partitions in your img file 
    You may use `sudo` to get permission

!!! Tip
    Use `fdisk -h` to learn more about the existing options

<!-- !!! Tip "1 - Solution"

    ```bash
    sudo fdisk -l imgs/2024-11-19-raspios-bookworm-arm64-lite.img
    ``` -->


You must get something like:
```bash
Disk imgs/2024-11-19-raspios-bookworm-arm64-lite.img: 2.57 GiB, 2755657728 bytes, 5382144 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x8a438930

Device                                           Boot   Start     End Sectors  Size Id Type
imgs/2024-11-19-raspios-bookworm-arm64-lite.img1         8192 1056767 1048576  512M  c W95 FAT32 (LBA)
imgs/2024-11-19-raspios-bookworm-arm64-lite.img2      1056768 5382143 4325376  2.1G 83 Linux
```

Get the starting point of the partitions contained in the image

Here, partition 1 (boot) starts at block 8192 and partition2 (rootfs) starts at block 1056768.  
The block size is 512 bytes.

**Check yours**

!!! Note
    **Why does the first partition start at sector 8192 ?**  
    In order to leave space at the beginning for :  

    - Bootloader data  
    - Alignment (important for SD cards)  
    
    This is common in Raspberry Pi images

We first create an access file to mount the main partition (partition 2) on our computer
```bash
sudo mkdir -p /mnt/rpi
```

!!! Note
    The `/mnt` mount directory is where external partitions are located when mounted on Linux Ubuntu.
    For example when you plug an external storage device (SSD, USB key, etc)


Then we will use the `mount` command to mount the partitions. This is
the standard way to mount a partition, i.e. to give access to the folders
and files on the partition.

!!! Note
    `mount` automatically detect the partition type (linux ext4, dos, ntfs, ...).

We mount partition 2 with the root file system
```bash
sudo mount -o loop,offset=$((1056768*512)) imgs/2024-11-19-raspios-bookworm-arm64-lite.img /mnt/rpi
```

We check that the file system of Raspberry PI OS is there :
```bash
ls /mnt/rpi
```

Then we unmount the partition
```bash
sudo umount /mnt/rpi
```

We create an access file to mount the other partition (partition 1)
```bash
sudo mkdir -p /mnt/rpi/boot
sudo mount -o loop,offset=$((8192*512)) imgs/2024-11-19-raspios-bookworm-arm64-lite.img /mnt/rpi/boot
```
We can display the content of the boot folder :
```bash
ls /mnt/rpi/boot
```

and we unmount the partition to cleanly exit
```bash
sudo umount /mnt/rpi/boot
sudo rmdir /mnt/rpi/boot
```

!!! Warning
    The main problem of this method is that we cannot mount simultaneously the two
    partitions at the right places `/mnt/rpi` and `/mnt/rpi/boot` because an overlapping
    error occurs.

## Second method (Recommended)

loop devices are virtual block devices that map a file to a disk-like interface. They let Linux treat a regular file as if it were a real disk or partition.

We search for a non used loop device (/dev/loop??), list existing loop devices :
```bash
lsblk
```

For example /dev/loop50 is not used on my computer, I will use it here.

!!! Note "2 - Issue"
    The `losetup` command allows controlling loop devices. Learn how to use the `-P` option in order to create a partitioned loop device, say `/dev/loop50` from your Raspberry Pi OS image.

<!-- !!! Tip "2 - Solution"

    ```bash
    sudo losetup -P /dev/loop50 imgs/2024-11-19-raspios-bookworm-arm64-lite.img
    ``` -->

The -P option find the 2 partitions and give them these names :  

- partition 1 : /dev/loop50p1 (name: boot)
- partition 2 : /dev/loop50p2 (name: rootfs)


!!! Note "3 - Issue"
    1. Create a folder to access the mount (already done before in method 1 !)
    2. Then, mount partition 2 with the root file system using `mount` (no need for the `-o` option)
    3. Finally, mount the boot partition at the right place in the root file system : look for a boot directory

!!! Tip
    You should use ```sudo mount <source> <directory>```


<!-- !!! Tip "3 - Solution"

    Create the folder to access the mount
    ```bash
    sudo mkdir -p /mnt/rpi
    ```

    Then mount the file system partition (rootfs)
    ```bash
    sudo mount /dev/loop50p2 /mnt/rpi
    ls /mnt/rpi
    ```

    and mount the boot partition
    ```bash
    sudo mount /dev/loop50p1 /mnt/rpi/boot
    ls /mnt/rpi/boot
    ``` -->

Finally, we unmount the partitions to cleanly exit
```bash
sudo umount /mnt/rpi/boot
sudo umount /mnt/rpi
sudo losetup -d /dev/loop50
```

!!! Warning
    To cleanly exit : make the last 3 commands, in the right order, do not
    execute the next command if the previous has failed.