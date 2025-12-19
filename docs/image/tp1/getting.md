# A. Getting the Raspberry Pi OS image

Create the working directory
```bash
mkdir rpilab && cd rpilab
```
Create a folder for the images
```bash
mkdir imgs && cd imgs
```

We choose the "lite" version of raspios `bookworm` (from 2024, November 19th)  
Download the image in the lab from the official website:  
[https://www.raspberrypi.com/software/operating-systems/](https://www.raspberrypi.com/software/operating-systems/)  
Click on "View archive" to get this version, or use the command-line :

```bash
wget https://downloads.raspberrypi.com/raspios_lite_arm64/images/raspios_lite_arm64-2024-11-19/2024-11-19-raspios-bookworm-arm64-lite.img.xz
```

We now check the checksum to avoid any problem during the download
```bash
wget https://downloads.raspberrypi.com/raspios_lite_arm64/images/raspios_lite_arm64-2024-11-19/2024-11-19-raspios-bookworm-arm64-lite.img.xz.sha256
```

We compute the checksum
```bash
sha256sum 2024-11-19-raspios-bookworm-arm64-lite.img.xz
```
We verify that the code is the same
```bash
cat 2024-11-19-raspios-bookworm-arm64-lite.img.xz.sha256
```
We can now safely decrompress the image
```bash
xz -d 2024-11-19-raspios-bookworm-arm64-lite.img.xz
```
We go back in the lab folder
```bash
cd ..
```