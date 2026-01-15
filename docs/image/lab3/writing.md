# A. Writing the Raspberry Pi OS image on the sd-card

In this part, we plan to write our Raspberry Pi OS image on a sd-card in order to use it on a real board.

!!! Warning
    Make sure to use the Raspberry Pi OS bookworm image you set up in Lab 1

## Headless Raspberry Pi

For convenience or to save space and money, we don't want to use additional hardware components such as a monitor, keyboard, or mouse to access our Raspberry Pi, especially with a desktopless OS such as our Lite version of Raspberry Pi OS.  
We call this screenless setup a headless Raspberry Pi.  

There are different ways to configure a headless Raspberry Pi, here are three :

- **Serial Terminal** - This requires extra hardware in the form of a serial-to-USB adapter, but it is by far the most robust way to connect, as you are not relying on any network setup.
- **Ethernet with Static IP Address** - This method requires a Linux operating system to change some files on the Raspberry Pi image. You can give the Raspberry Pi a static IP address and then use an Ethernet cable (or WiFi) to log in.
- **WiFi with DHCP** - You will need to have access to your router to find your Raspberry Pi's IP address in order to log in via SSH. As a result, this may not be the best option in school or office environments.


If you would like to access your Raspberry Pi using the least amount of software work or without network, you will need some extra hardware. Two of the pins on the Raspberry Pi offer transmit and receive data for serial communication. With a small change to a file on the boot sector of the SD card, a command line terminal will be broadcast over this serial line, and you can enter commands to control Linux, write programs, etc.

If the other methods do not work to gain access to your Raspberry Pi or you lose your video out signal, using the serial terminal is a great way to see if your Raspberry Pi is still working and to debug any problems you might have.

You will need a USB to serial converter for this to work.

![](../../img/serial-to-usb.png)

## Enable serial interface and login shell over serial

In versions of the Raspberry Pi after 3 (e.g. 3 Model B, 3 Model B+, Zero W), the processor contains two hardware UARTs. One is dedicated to the Bluetooth module, while the other is a less-featured "mini UART."

The serial interface is not enabled by default on the board, but we have already enabled it in Lab 1.

!!! Warning
    If Lab 1 made you use `raspi-config` to enable UART, it may have failed even if your setup was correct.  
    In fact, `raspi-config` needs a real Raspberry Pi kernel to work well, and QEMU is not sufficient.  
    But good news, most things `raspi-config` does are just file edits, so we can do it safely manually.

We will make sure the serial interface is enabled :  

- Mount the image using
```bash
sudo losetup -P /dev/loop50 imgs/2024-11-19-raspios-bookworm-arm64-lite.img
sudo mount /dev/loop50p2 /mnt/rpi
sudo mount /dev/loop50p1 /mnt/rpi/boot
```
- Look for a `config.txt` file in `/boot` and open it.
- Now check there is the line `enable_uart=1` under the `[all]` section, if that's not the case add it, save and exit.
- Unmount the image :
```bash
sudo umount /mnt/rpi/boot
sudo umount /mnt/rpi
sudo losetup -d /dev/loop50
```

The sd-card can now be unmounted from your computer.

## Choose an Imager

We need to choose an imager and download it.
Raspberry Pi Imager and Balena Etcher are the go-to imagers.

**Raspberry Pi Imager** is designed for Raspberry Pi users :

- Easily pick Raspberry Pi OS or other OSes from the app itself.
- Zero-fuss experience built specifically around Pi usage.
- Built-in downloads and automatic image selections.
- Supports writing local image files
    

**Balena Etcher** offers a broad compatibility and reliability :

- Write any OS image (Ubuntu, Fedora, RetroPie, custom ISOs, etc.)
- Verification after writing to reduce corrupted cards
- Doesn't offer an OS selection in the app, it needs local image file


As we are using a custom image, we will use Balena Etcher which is designed to work with custom images and verifies the write integrity to avoid any corruption.

!!! Note
    You could use Raspberry Pi Imager too as it supports writing local image files but it doesn't verify the image after writing.
    You would prefer using it when you want an official OS for Raspberry Pi and built-in options.  

## Write the image

![](../../img/etcher.png)

Download the software from the official website: [https://etcher.balena.io/](https://etcher.balena.io/)

Insert the sd-card in your computer.

!!! Warning
    You might need to use a sd-to-usb adapter if an error occurs when writing the image

Finally, launch Balena Etcher and follow the the process to write the image on the sd-card.  
After a few minutes, the copy should be done and the sd-card ready to be inserted in the Raspberry PI (RPI) board.

