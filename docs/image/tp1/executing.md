# E. Executing commands on the Raspberry Pi OS image

## Introduction

Changing values in configuration files in the image on host is
fairly easy (as seen before)  
But what about running a command in the image on host?
for example to change the password ?

!!! Warning "Problem 1"
    Host executes X86 code while image contains ARM executables  
    *Solution* : emulating ARM on X86 with QEMU (created by
    Fabrice Bellard)  
    --> Read [Introduction to QEMU](../qemu.md)

!!! Warning "Problem 2"
    Host commands are under / (ex /bin/bash) while
    image commands are under /mnt/rpi (ex
    /mnt/rpi/bin/bash).  
    *Solution* : changing the root (/) using chroot, introduced
    in development of Version 7 Unix in 1979.

## Starting chroot

### Standard process

Prepare the Raspberry Pi OS image to be executed it on host ...
First we need to mount the system folder the same way they are
mounted on the real Raspberry Pi board

Mount the image in a loop device (we use /dev/loop50 as before)
```bash
sudo losetup -P /dev/loop50 imgs/2024-11-19-raspios-bookworm-arm64-lite.img
```

Mount the partitions (as before)
```bash
sudo mount /dev/loop50p2 /mnt/rpi
sudo mount /dev/loop50p1 /mnt/rpi/boot
```

Mount the system folders to make them similar as
if we were executing on the actual Raspberry board
for example, /dev is an important folder that gives
access to all the devices : /mnt/rpi/dev must be translated to /dev
```bash
sudo mount --bind /dev /mnt/rpi/dev/
```

!!! Note "1 - Issue"
    Do the same for all the other system folders we need :
    
    - `/sys`, which is an interface to the kernel
    - `/proc`, which is a virtual file system working as an interface to ongoing processes

!!! Tip "1 - Solution"

    ```bash
    sudo mount --bind /sys /mnt/rpi/sys/
    sudo mount --bind /proc /mnt/rpi/proc/
    ```

!!! Note
    A PTY (pseudo-terminal) is a kernel feature that lets programs behave as if they are attached to a real terminal.
    Examples of things that require PTYs:

    - interactive shells (bash, sh)
    - sudo
    - ssh
    - su
    - screen, tmux
    - apt / dpkg (often indirectly)
    - systemctl

    So in a chroot, no PTYs = lots of weird failures.

PTYs (/dev/pts/*), pseudo-terminals are provided by the devpts filesystem. Mount it using :
```bash
mkdir -p /mnt/rpi/dev/pts
sudo mount -t devpts devpts /mnt/rpi/dev/pts
sudo ln -sf pts/ptmx /mnt/rpi/dev/ptmx
```

!!! Note
    We need to disable some specific actions done solely on the
    real board. The `/etc/ld.so.preload` file tells what libraries should be
    loaded before the other. This does not work on the virtual
    Raspberry Pi.

    On modern Debian-based systems (including Raspberry Pi OS Bookworm = Debian 12) :

    - `/etc/ld.so.preload` is optional
    - It is not created by default
    - The dynamic linker only reads it if it exists

    The file `/etc/ld.so.preload` should only be executed on actual board.  
    Check if there is such a file :
    ```bash
    ls /mnt/rpi/etc/ld.so.preload
    ```
    It should return nothing. If it returns `/mnt/rpi/etc/ld.so.preload`, it means you are using an old version, then for safety keep a backup version and comment the content of `/mnt/rpi/etc/ld.so.preload`.

We also need to add QEMU to translate ARM code to X86 64
code so it can be executed on the host computer. QEMU is added
by just copying the emulator in the Raspberry Pi OS image :
```bash
sudo cp /usr/bin/qemu-arm-static /mnt/rpi/usr/bin/
```

!!! Warning
    Make sure you installed QEMU following [Introduction to QEMU](../qemu.md) instructions

### Using host network in chroot

For some applications, we may need access to Internet.  
A practical example is the use of the packet manager with apt

We need to copy some of the host configuration files (here
/etc/resolv.conf) on the image before running chroot

First save the current config as a backup
```bash
sudo cp /mnt/rpi/etc/resolv.conf /mnt/rpi/etc/resolv.conf.bck
```
Copy config from host
```bash
sudo cp /etc/resolv.conf /mnt/rpi/etc/resolv.conf
```

Now all should be prepared correctly.

### start_chroot.bash

To avoid reapeating the previous series of commands and make more easy the use of chroot we can create a bash script
to run before chroot : `start_chroot.bash`.


Example of a starting script. Create the file `start_chroot.bash` in rpilab.
```bash
nano start_chroot.bash
```

And copy the following :
```bash
#!/bin/bash

# command parameter ($1) is the path to the RPI image file
# example of use :
# source start_chroot.bash imgs/2024-11-19-raspios-bookworm-arm64-lite.img
sudo losetup -P /dev/loop50 $1
sudo mount /dev/loop50p2 /mnt/rpi
sudo mount /dev/loop50p1 /mnt/rpi/boot
sudo mount --bind /dev /mnt/rpi/dev/
sudo mount --bind /sys /mnt/rpi/sys/
sudo mount --bind /proc /mnt/rpi/proc/
sudo mount -t devpts devpts /mnt/rpi/dev/pts
sudo ln -sf pts/ptmx /mnt/rpi/dev/ptmx
sudo cp /usr/bin/qemu-arm-static /mnt/rpi/usr/bin/
sudo cp /mnt/rpi/etc/resolv.conf /mnt/rpi/etc/resolv.conf.bck
sudo cp /etc/resolv.conf /mnt/rpi/etc/resolv.conf
```

## Run chroot

**We can now chroot and run the RPI image as if it was on a actual board !**

`/mnt/rpi` will be our new `/` and we execute /bin/bash to start a terminal

!!! Note "2 - Issue"
    Use `chroot` to make `/mnt/rpi` the new root and start a bash terminal

!!! Tip
    The `/bin/bash` command is used to open a new bash shell

<!-- !!! Tip "2 - Solution"

    ```bash
    sudo chroot /mnt/rpi /bin/bash
    ``` -->

We have now a root prompt # and we can execute commands on
the RPI image, for example :

- to create an new user ue41 with root privileges (sudo)
- set locale to en US.UTF-8 using raspi-config (5 Localisation options)
- set keyboard to French layout using raspi-config
- to enable the console with raspi-config (to access the actual RPI board without
network) for future use with real board (may be done later)
- run apt update

!!! Tip
    To leave the `chroot` shell, use  `Ctrl + D`

!!! Note
    With some experience, we can do most of the setup of the RPI
    image without using the actual board !!!

## Adding a sudo user with chroot

To do this, we make sure we are in chroot (# prompt) on the
Raspberry PI OS, and we will proceed in two steps :

We will create a new user called ue41, password ue41.  
(no need to answer all the
questions !!! most of them can be left empty)

!!! Note "3 - Issue"
    Use ``adduser`` to create the ue41 user

<!-- !!! Tip "3 - Solution"

    ```bash
    adduser ue41
    ``` -->

Execute `visudo` command to give sudo permission to the
created user
```bash
visudo
```
A file should open.  
Add a line with ue41 user here :
```
%sudo   ALL=(ALL:ALL) ALL
ue41    ALL=(ALL:ALL) ALL
```

Then write and save (nano commands)

## Enabling the RPI console for debug with chroot

The console is an easy tool for debug, it can be connected to the host via FTDI USB
cable and can be accessed on Ubuntu via /dev/ttyUSB0 device. Hence, it can work
even if the network is not or badly defined.  
To enable the console, Raspberry Pi OS offers several ways :

- adding the line enable uart=1 at the end of the file /mnt/rpi/boot/config.txt
with standard mount of boot partition,
- adding the line enable uart=1 at the end of the file boot/config.txt in chroot
mode,
- use raspi-config in chroot mode.

As we have started chroot, we will use the raspi-config tool.

Simply execute raspi-config under chroot prompt `#`
```bash
raspi-config
```

Then go in `Interface Options` and enable `Serial Port`. 

More details on [raspi-config Documetation](https://www.raspberrypi.com/documentation/computers/configuration.html)

!!! Note
    raspi-config can be used to easily configure a lot of features such as the network, the locale language, etc

## Clean termination of chroot

### Standard process

To exit chroot, just type `ctrl+D` or `exit`.

To return to a standard host configuration we have to :

- go back to the original network config if we used the host's
- uncomment actions in /mnt/rpi/etc/ld.so.preload if we did so
- unmount all the RPI image system folders
- stop the loop device with RPI image

Restore the network config
```bash
sudo cp /mnt/rpi/etc/resolv.conf.bck /mnt/rpi/etc/resolv.conf
```

Unmount all volumes at once
```bash
sudo umount /mnt/rpi/{dev/pts,dev,sys,proc,boot,}
```
Stop the loop device with RPI image
```bash
sudo losetup -d /dev/loop50
```


### stop_chroot.bash

Just like we did for starting chroot we can create a bash script to run after chroot: `stop_chroot.bash`

!!! Note "4 - Issue"
    Write a `stop_chroot.bash` script to cleanly return to the standard host configuration.

<!-- !!! Tip "4 - Solution"

    Create the file `stop_chroot.bash`.
    ```bash
    nano stop_chroot.bash
    ```
    And copy the following :
    ```bash
    #!/bin/bash

    # usage :
    # source stop_chroot.bash
    sudo cp /mnt/rpi/etc/resolv.conf.bck /mnt/rpi/etc/resolv.conf
    sudo umount /mnt/rpi/{dev/pts,dev,sys,proc,boot,}
    sudo losetup -d /dev/loop50
    ``` -->


## Run apt command in chroot

Now with the scripts you should be able to only use the following :

```bash
source start_chroot.bash imgs/2024-11-19-raspios-bookworm-arm64-lite.img
sudo chroot /mnt/rpi /bin/bash
apt update
# type ctrl-D to exit chroot
source stop_chroot.bash
```


**Congrats ! You successfully ended this lab !**

Keep your modified image on your computer as it will be used in a next lab.