# A. Getting the Raspberry Pi OS image

!!! Warning
    Unfortunately, there is still no working QEMU emulation specific kernel for Bookworm at the time of this Lab
    So, in order to learn how to emulate a Raspberry Pi with QEMU VM, we will download a Buster image.  

    However, make sure to keep your Bookworm image from Lab 1 as we will use it in Lab 3

Follow the instructions in [Getting the Raspberry Pi OS image](../lab1/getting.md)
from the previous lab to download and decompress
the Raspberry PI OS Lite Buster version from 2021/05/07 (or 2021/05/08).

```bash
cd rpilab/imgs
wget https://downloads.raspberrypi.com/raspios_lite_arm64/images/raspios_lite_arm64-2021-05-28/2021-05-07-raspios-buster-arm64-lite.zip
unzip raspios_lite_arm64-2021-05-28/2021-05-07-raspios-buster-arm64-lite.zip
cd ..
```