# C. Using QEMU Virtual Machine

## Starting QEMU VM

Raspberry Pi OS images do not boot directly in QEMU without:

- A custom kernel
- Correct DTB (a binary hardware description file that tells the Linux kernel which hardware it is running on)
- Proper machine type

In `qemu-rpi-kernel` you will find kernels built specifically for QEMU emulation. As you can see, these don't go until the Bookworm version,
this is why we use a Buster image

The first time, we start with a shell (console /bin/bash) to
configure the execution of the VM.

```bash
qemu-system-arm \
-kernel qemu-rpi-kernel/kernel-qemu-4.19.50-buster \
-cpu arm1176 \
-m 256 \
-M versatilepb \
-no-reboot \
-serial stdio \
-append "root=/dev/sda2 panic=1 rootfstype=ext4 rw init=/bin/bash" \
-drive "file=imgs/2021-05-07-raspios-buster-armhf-lite.img,index=0,media=disk,format=raw" \
-dtb qemu-rpi-kernel/versatile-pb-buster.dtb
```

!!! Tip
    Look for the default login and password on a Rapsberry Pi

We add a special rule to have the disk devices named /dev/sd?
instead of /dev/mmcblk? (simpler naming for the disk drives)

Create the file rule file /etc/udev/rules.d/90-qemu.rules
```bash
cat << EOF > /etc/udev/rules.d/90-qemu.rules
KERNEL=="sda", SYMLINK+="mmcblk0" KERNEL=="sda?", \
SYMLINK+="mmcblk0p%n" KERNEL=="sda2", SYMLINK+="root"
EOF
```

Terminate the execution of VM by typing ctrl-D

## Executing QEMU VM

We would like to be able to access the QEMU VM with ssh

**Problem** : the VM has localhost IP address, same as host.  
To solve it, we use port mapping :  
tcp port 10022 of the host will be mapped to port 22 on the QEMU VM  
Then ssh will work using port 10022 and localhost

```bash
qemu-system-arm \
-kernel qemu-rpi-kernel/kernel-qemu-4.19.50-buster \
-cpu arm1176 \
-m 256 \
-M versatilepb \
-no-reboot \
-serial stdio \
-append "root=/dev/sda2 panic=1 rootfstype=ext4 rw" \
-drive "file=imgs/2021-05-07-raspios-buster-armhf-lite.img,index=0,media=disk,format=raw" \
-dtb qemu-rpi-kernel/versatile-pb-buster.dtb \
-net nic -net user,hostfwd=tcp::10022-:22
```

The QEMU VM is ready to be used with 3 possible access : QEMU
window, console and ssh.

!!! Tip
    To be untrapped in QEMU window, type ctrl-alt-g 

But there is still one step missing for ssh to work.

## Setting up network for ssh access

The RPI image (even the lite one) has all softwares installed to
access it via ssh.
However, port 22 is not accessible by default !

We can enable ssh by adding an empty file simply called ssh in the
boot folder

!!! Note "1 -Issue"
    Use the QEMU VM console with user pi to create the /boot/ssh file with `touch`

!!! Tip "1 - Solution"
    Start QEMU with a shell :
    ```bash
    qemu-system-arm \
    -kernel qemu-rpi-kernel/kernel-qemu-4.19.50-buster \
    -cpu arm1176 \
    -m 256 \
    -M versatilepb \
    -no-reboot \
    -serial stdio \
    -append "root=/dev/sda2 panic=1 rootfstype=ext4 rw init=/bin/bash" \
    -drive "file=imgs/2021-05-07-raspios-buster-armhf-lite.img,index=0,media=disk,format=raw" \
    -dtb qemu-rpi-kernel/versatile-pb-buster.dtb
    ```

    and use 
    ```bash
    sudo touch /boot/ssh
    ```

    then leave QEMU

!!! Note
    You can also use chroot with your bash scripts `start_chroot.sh` and `stop_chroot.sh`

After restarting the qemu vm, the ssh command should work fine.

!!! Note "2 -Issue"
    On an other terminal, connect to the QEMU VM from the host via ssh
    Look for the relevant ssh option to use the right port which is not the default 22.

!!! Tip
    The ip address is your localhost ipaddress so you can use the shortcut `pi@localhost`

!!! Tip "2 - Solution"
    ```bash
    ssh -p 10022 pi@localhost
    ```

You can stop QEMU and delete the Buster image after this lab.
