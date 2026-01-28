# B. Get the OS Image

## 1. Our needs

First, we need to choose the proper operating system for our application. Let’s start from our requirements:

- The robot is equipped with a **Raspberry Pi 5**, so the OS must be compatible  
- We want an **open-source or free Linux-based OS**  
- We plan to use **ROS 2 libraries and tools** to develop our drivers and controllers  

!!! Note "Question 1"
    The [ROS 2 on Raspberry Pi](https://docs.ros.org/en/rolling/How-To-Guides/Installing-on-Raspberry-Pi.html) documentation presents multiple solutions.  
    **Which configuration is preferred?**

!!! Tip
    Running ROS 2 in Docker on an embedded controller is not the preferred solution, as it adds extra software layers (which can be heavy). It is typically used when Ubuntu cannot be installed directly, or when multiple ROS versions must run side-by-side.

There are multiple ROS 2 distributions (versioned sets of ROS packages). How do we choose between them?  
We do not always select the newest distribution, as it may still be in active development. Instead, we usually choose a **LTS (Long-Term Support)** distribution.

!!! Note "Question 2"
    Unless our application has specific requirements, we choose the LTS distribution with the **latest EOL (End Of Life)** date.  
    **Which ROS 2 distribution should we use?**  
    See: [ROS 2 Distributions](https://docs.ros.org/en/rolling/Releases.html)

---

## 2. Choose the OS

Ubuntu provides several editions:

- Ubuntu Desktop  
- Ubuntu Server  
- Ubuntu Core  
- ...

Since our target computer is an embedded, headless Raspberry Pi, we do not need a desktop environment.

**Ubuntu Server** is lightweight and runs without a graphical interface, which saves computing resources on an embedded system. It is designed for reliable headless operation over SSH, which matches how robots are accessed and managed in the field. It is also widely supported by robotics tools such as ROS, making software integration easier.

Therefore, we will use **Ubuntu Server**.

Next, there are two main ways to install ROS 2:

- Using precompiled binary packages  
- Building from source  

We will install ROS 2 using the official binary packages in order to get a working setup quickly.  
However, ROS 2 binary packages are only provided for specific Ubuntu versions.

!!! Note "Question 3"
    **What is the target Ubuntu version for your chosen ROS 2 distribution?**  
    Find this information on the installation page for your ROS 2 distribution.

!!! Tip
    **Ubuntu 20.04, 22.04, 24.04, 25.10**

---

## 3. Download the OS Image

Place yourself in the `ubuntulab` folder

Ubuntu Server images for Raspberry Pi are available here: [Install Ubuntu on a Raspberry Pi](https://ubuntu.com/download/raspberry-pi)

Look for the proper Ubuntu version either on the default installer or in the archives: [Ubuntu releases](https://cdimage.ubuntu.com/releases/).  

!!! Warning
    If you search your version in the archives, make sure to choose the Raspberry Pi preinstalled Server image.

Download Ubuntu Server. You must obtain a `.img.xz` file.  
Move the file in `ubuntulab`

!!! Warning
    Don't save your image in your local git repo or use a `.gitignore` so you don't push it online, as it is heavy and unecessary.


!!! Note "Question 4"
    **Decompress the xz file with the `xz` command**


## 4. Increase the size of the .img file

!!! Note "Question 5"
    Check your image and partitions sizes with `fdisk -l`

Later, we will install ROS2 on our image but we see that our img is only a bit larger than 3G which is too small for system files and ROS2 installation.

Let's increase it to 8G :

```bash
sudo truncate -s 8G <your_.img_file>
```

!!! Warning
    It requires some space on your computer

The image has been resized but not he partition.  
Now use `fdisk` to resize the root partition

```bash
sudo fdisk <your_image>
```

Inside fdisk:

- Press p → see current partitions
- Note start sector of partition 2 (in our case, it is probably 1050624)
- Press d → delete partition 2 (don’t worry, data is safe because we will recreate it at the same start sector)
- Press n → new partition

    - Type primary (p)
    - Partition number: 2
    - First sector: 1050624 (must be same as before!)
    - Last sector: accept default (fill the image)
    - Keep `ext4` signature

- Press w → write changes

Check your partitions sizes again. It must have increased.


!!! Tip
    You can delete the .img file after you completely finished the lab