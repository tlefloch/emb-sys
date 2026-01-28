# G. ROS 2 Setup

Now, let's install ROS 2.

Remember: we can execute commands inside the image and use our PC’s shared internet access.

For example, update the package index:

```bash
sudo apt update
```

We will install ROS 2 binary packages inside the chroot. This is simpler and faster than building from source.

!!! Note "Question 1"
    Follow the ROS 2 documentation corresponding to your chosen ROS 2 distribution to install ROS 2 using **deb packages**.  

    You will have to choose between the **Desktop Install** and the **ROS-Base Install**.  
    Since we are setting up a headless Raspberry Pi, GUI tools are not required, so select the **minimal ROS-Base install**.

    Copy the commands you used in your answer file.


!!! Warning
    As we are emulating an arm64 archietcture from an x86_64 architecure, these steps may be quite long.  
    Skip it if it takes too much time.