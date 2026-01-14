# A. Writing the Raspberry Pi OS image on the sd-card

In this part, we plan to writing our Raspberry Pi OS image on a sd-card in order to use it on a real board.

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

