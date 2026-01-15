# Debug

This section tries to solve some of the problems encountered with the students during the lab

## Nothing appears in minicom

1. Make sure you are in the `dialout` group in the terminal from which you launch minicom  
Before starting minicom, check using the following command : `groups`  
If `dialout` doesn't appear, refer to the **Set-up the FTDI USB cable connection on a serial port of the host** section in [B. Accessing the headless RPI without network](../lab3/connecting.md) section to solve this.

2. Make sure the wiring is correct. In UART, Rx goes on Tx and Tx on Rx.

3. The sd-card must be plugged in the RPI in the sd port, not with a usb adapter

4. Make sure there is the line `enable_uart=1` at the end of the `/boot/config.txt` of your Raspberry Pi OS image. If not, refer to the **Enable serial interface and login shell over serial** section in [A. Writing the Raspberry Pi OS image on the sd-card](../lab3/writing.md) section to solve this.

5. Make sure you are using the Raspberry Pi OS Bookworm image from Lab 1 on the sd card




If nothing of these solves the issue, the cause is probably hardware either from your computer (ex: failing usb port) or from the RPI setup (ex: failing FTDI USB, failing sd-card, ...)

## You can't access or write on the sd-card when it is plugged on your computer

Try formatting the card, if you can't don't insist, the sd card is probably dead.

## You can't use nmcli

Make sure you are using the Raspberry Pi OS Bookworm (or newer) image from Lab 1 on the sd card
Older versions of Raspberry Pi OS don't use nmcli but `/etc/wpa_supplicant.conf` and `/etc/dhcpch.conf` files.