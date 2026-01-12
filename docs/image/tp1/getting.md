# A. Getting the Raspberry Pi OS image

Create the working directory
```bash
mkdir rpilab && cd rpilab
```
Create a folder for the images
```bash
mkdir imgs && cd imgs
```

Let's choose the "lite" version of raspios `bookworm` (from 2024, November 19th)

!!! Note "1 - Issue"

    Download the image in the lab from the official website:  
    [https://www.raspberrypi.com/software/operating-systems/](https://www.raspberrypi.com/software/operating-systems/)  
    Click on "View archive" to get this version and download it using the `wget` command.


!!! Tip "1 - Solution"

    ```bash
    wget https://downloads.raspberrypi.com/raspios_lite_arm64/images/raspios_lite_arm64-2024-11-19/2024-11-19-raspios-bookworm-arm64-lite.img.xz
    ```

Now, we want to check the checksum to ensure data integrity and avoid any problem during the download

!!! Note "2 - Issue"
    1. Download the cheksum file (`.sha256` file) using `wget`  
    2. Compute the checksum using the `sha256sum` command  
    3. Verify that the resulting code is the same as the one contained in the `.sha256` file, using `cat` to display the file's content in the shell

!!! Tip "2 - Solution"

    First, we download the checksum file

    ```bash
    wget https://downloads.raspberrypi.com/raspios_lite_arm64/images/raspios_lite_arm64-2024-11-19/2024-11-19-raspios-bookworm-arm64-lite.img.xz.sha256
    ```

    Then, we compute the checksum

    ```bash
    sha256sum 2024-11-19-raspios-bookworm-arm64-lite.img.xz
    ```

    Finally, we check that the code is the same

    ```bash
    cat 2024-11-19-raspios-bookworm-arm64-lite.img.xz.sha256
    ```

!!! Note "3 - Issue"
    We can now safely decrompress the image using `xz`.

!!! Tip "3 - Solution"

    ```bash
    xz -d 2024-11-19-raspios-bookworm-arm64-lite.img.xz
    ```

We go back in the lab folder
```bash
cd ..
```