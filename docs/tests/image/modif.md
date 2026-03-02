# C. Modifying the Image

## 1. Method Choice

You downloaded a preinstalled Ubuntu Server image for Raspberry Pi:  
`ubuntu-<version_number>-preinstalled-server-arm64+raspi.img`

This image is built for an **ARM64 processor**, while your computer likely uses an **x86_64 (amd64)** CPU.  
Because of this architecture difference, the image cannot run natively on your PC.

To configure it anyway, we must emulate an ARM system.

There are two main methods:

---

### Method 1 — Modify the Image Using **chroot + QEMU** (Lightweight, Fast)

This method allows you to enter the Raspberry Pi filesystem directly from your PC and run ARM programs using CPU emulation.

**Principle:**

- Mount the `.img` file as a disk  
- Access its Linux filesystem  
- Use QEMU user emulation to run ARM binaries  
- Enter the system using `chroot`  
- Configure it like a real Raspberry Pi OS  

**Pros:**

- Fast — no full system boot  
- Lightweight — no virtual machine overhead  
- Directly edits the real SD card image  

**Cons:**

- Not a fully booted system  
- Hardware-related services will not function  

For headless Raspberry Pi preparation, this method is ideal.  
It is the most realistic embedded Linux preparation workflow used in robotics and industry.

---

### Method 2 — Boot the Image in a Full QEMU Virtual Machine (Heavier, More Realistic)

Instead of modifying files directly, you can emulate a full Raspberry Pi–like machine and boot the image.

**Principle:**

- QEMU emulates ARM hardware  
- The `.img` file is used as the virtual SD card  
- You interact with the system as if it were a real Pi (terminal login)

**Pros:**

- Real boot process  
- `systemd` runs normally  

**Cons:**

- Slow  
- Complex setup  
- Raspberry Pi images do not always boot cleanly in generic QEMU without kernel modifications  
- The emulated hardware may not exactly match the real device  

We use a QEMU VM when we need a fully booted system, for example to test:

- Boot errors  
- Service startup issues  
- Login behavior  
- Networking configuration  

---

## 2. Setup the Image for chroot

The following steps are similar to those performed in Lab 1.

!!! Warning
    Do **not** reuse your `start_chroot.sh` and `stop_chroot.sh` scripts from Lab 1.  
    Some steps differ in this lab, and using the old scripts may cause errors.

---

!!! Note "Question 1"
    **Attach the image as a loop device using `losetup`**, using options that allow you to:

    - Create a partitioned loop device  
    - Automatically select the first free loop device  
    - Print the device name after setup  

!!! Tip "Answer 1"

    Command :

    ```bash
    sudo losetup -P --find --show ubuntu-24.04.3-preinstalled-server-arm64+raspi.img
    ```

    Example output :

    ```bash
    /dev/loop6
    ```

---

!!! Note "Question 2"
    **Mount the root filesystem partition**

    1. Use `lsblk` to identify which partition from your newly created loop device is the largest — this is the root filesystem (copy the output in your answer file).  
       It will look like `/dev/loopXp1` or `/dev/loopXp2`.  
    2. Create a mount point:
    ```bash
    sudo mkdir -p /mnt/rpi
    ```
    3. Mount the root filesystem partition at `/mnt/rpi`.

!!! Tip "Answer 2"
    
    Command :

    ```bash
    lsblk
    ```

    Example output:

    ```bash
    NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
    loop0         7:0    0     4K  1 loop /snap/bare/5
    loop1         7:1    0  55.5M  1 loop /snap/core18/2979
    loop2         7:2    0  73.9M  1 loop /snap/core22/2216
    loop3         7:3    0    74M  1 loop /snap/core22/2292
    loop4         7:4    0  55.5M  1 loop /snap/core18/2976
    loop5         7:5    0  66.8M  1 loop /snap/core24/1267
    loop6         7:6    0     8G  0 loop 
    ├─loop6p1   259:7    0   512M  0 part 
    └─loop6p2   259:8    0   7.5G  0 part 
    loop7         7:7    0 251.7M  1 loop /snap/firefox/7720
    loop8         7:8    0 252.1M  1 loop /snap/firefox/7633
    loop9         7:9    0  18.5M  1 loop /snap/firmware-updater/210
    loop10        7:10   0  16.4M  1 loop /snap/firmware-updater/216
    loop11        7:11   0   516M  1 loop /snap/gnome-42-2204/202
    loop12        7:12   0 516.2M  1 loop /snap/gnome-42-2204/226
    loop13        7:13   0  91.7M  1 loop /snap/gtk-common-themes/1535
    loop14        7:14   0 448.9M  1 loop /snap/kf5-5-105-qt-5-15-9-core22/11
    loop15        7:15   0 450.2M  1 loop /snap/kf5-5-108-qt-5-15-10-core22/5
    loop16        7:16   0 877.5M  1 loop /snap/plotjuggler/174
    loop17        7:17   0 599.3M  1 loop /snap/plotjuggler/182
    loop18        7:18   0  10.8M  1 loop /snap/snap-store/1270
    loop19        7:19   0  50.9M  1 loop /snap/snapd/25577
    loop20        7:20   0  10.8M  1 loop /snap/snap-store/1248
    loop21        7:21   0  48.1M  1 loop /snap/snapd/25935
    loop22        7:22   0   576K  1 loop /snap/snapd-desktop-integration/343
    loop23        7:23   0 226.3M  1 loop /snap/thunderbird/915
    loop24        7:24   0   576K  1 loop /snap/snapd-desktop-integration/315
    loop25        7:25   0 226.4M  1 loop /snap/thunderbird/919
    loop26        7:26   0 321.1M  1 loop /snap/vlc/3777
    loop27        7:27   0  66.9M  1 loop /snap/core24/1349
    nvme0n1     259:0    0 953.9G  0 disk 
    ├─nvme0n1p1 259:1    0   500M  0 part /boot/efi
    ├─nvme0n1p2 259:2    0   128M  0 part 
    ├─nvme0n1p3 259:3    0 493.7G  0 part 
    ├─nvme0n1p4 259:4    0   9.5G  0 part 
    ├─nvme0n1p5 259:5    0  15.7G  0 part [SWAP]
    └─nvme0n1p6 259:6    0 434.3G  0 part /
    ```

    The largest partition is `/dev/loop6p2`

    Create the mount foler:

    ```bash
    sudo mkdir -p /mnt/rpi
    ```

    Mount the root filesystem partition

    ```bash
    sudo mount /dev/loop6p2 /mnt/rpi
    ``` 

---

On Ubuntu for Raspberry Pi, the FAT boot partition is mounted at `/boot/firmware`, not `/boot`.

Ubuntu separates:

- `/boot` → Linux kernel, initrd, and bootloader files managed by Ubuntu packages  
- `/boot/firmware` → Raspberry Pi firmware partition (FAT) containing files such as `config.txt`, `cmdline.txt`, `start*.elf`, etc.

When preparing the image offline, mount the boot partition at `/mnt/rpi/boot/firmware` so it matches the layout expected at runtime.

!!! Tip
    If the `firmware` folder does not exist inside `/mnt/rpi/boot`, create it:

    ```bash
    sudo mkdir -p /mnt/rpi/boot/firmware
    ```

---

!!! Note "Question 3"
    **Mount the boot partition** at `/mnt/rpi/boot/firmware`.

!!! Tip "Answer 3"

    ```bash
    sudo mount /dev/loop6p1 /mnt/rpi/boot/firmware
    ```

---

Do not forget to mount system directories (required for `chroot`):

```bash
sudo mount --bind /dev /mnt/rpi/dev
sudo mount --bind /dev/pts /mnt/rpi/dev/pts
sudo mount --bind /proc /mnt/rpi/proc
sudo mount --bind /sys /mnt/rpi/sys
```

!!! Note
    If you get PTY errors, restart your PC and try instead :

    ```bash
    sudo mount --bind /dev /mnt/rpi/dev/
    sudo mount --bind /sys /mnt/rpi/sys/
    sudo mount --bind /proc /mnt/rpi/proc/
    sudo mount -t devpts devpts /mnt/rpi/dev/pts
    ```

    If the error persists, try omitting the `/mnt/rpi/dev/pts` mount.

Then, **Enable ARM emulation inside the image with QEMU** :

```bash
sudo cp /usr/bin/qemu-aarch64-static /mnt/rpi/usr/bin/
```

Finally, bind your host’s `/etc/resolv.conf` inside the chroot :

```bash
sudo cp /etc/resolv.conf /mnt/rpi/etc/resolv.conf
```

!!! Note
    If you get a symlink error, just delete /mnt/rpi/etc/resolv.conf and try again

It will allow to access internet through the host in chroot.

!!! Tip
    You may want to group the previous steps into a bash script to execute them in a single command, as you did in Lab 1.

Finally, the setup is done and we are ready to run `chroot`

!!! Note "Question 4"
    **Enter the Raspberry Pi system using `chroot`**

!!! Tip "Answer 4"
    ```bash
    sudo chroot /mnt/rpi /bin/bash
    ```

Test the emulation is working using
```bash
uname -m
```

It should return `aarch64`.

