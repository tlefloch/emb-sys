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

Test the emulation is working using
```bash
uname -m
```

It should return `aarch64`.

