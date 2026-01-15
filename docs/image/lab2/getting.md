# A. Getting the Raspberry Pi OS image

!!! Warning
    Unfortunately, there is still no working QEMU emulation specific kernel for Bookworm at the time of this Lab
    So, in order to learn how to emulate a Raspberry Pi with QEMU VM, we will download a Buster image.  

    However, make sure to keep your Bookworm image from Lab 1 as we will use it in Lab 3

!!! Note "1 - Issue"
    Follow the instructions in [Getting the Raspberry Pi OS image](../lab1/getting.md)
    from the previous lab to download and decompress
    the Raspberry PI OS Lite Buster version from 2021/05/07 (or 2021/05/08).

!!! Tip "1 - Solution"

    ```bash
    cd rpilab/imgs
    wget https://downloads.raspberrypi.org/raspios_lite_armhf/images/raspios_lite_armhf-2021-05-28/2021-05-07-raspios-buster-armhf-lite.zip
    unzip 2021-05-07-raspios-buster-armhf-lite.zip
    cd ..
    ```