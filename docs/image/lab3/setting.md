# C. Setting up network for ssh access

## Recap : 4 ways to access the RPI without network

We have now 4 ways to setup the RPI image without network, for
example to set the network parameters.  
Note that all these 4 ways do not require the use of a display
connected on the HDMI plug and mouse+keyboard connected on
the USB plugs of the RPI BOARD

1. mount the RPI image (raspios bookworm) on host as a standard
file system to modify configuration files
2. chroot on the RPI sd-card image on host to directly execute
RPI configuration commands on host
3. QEMU virtual machine running the RPI image from the
sd-card
4. RPI console (terminal) on the actual RPI board via the FTDI
USB cable, accessed with minicom (or similar tools) on the
host

If you do not want to use the serial terminal or want to be able to log in to your Raspberry Pi over a network connection, you can give your Pi a static IP address, connect it to your network (or to your computer via a crossover/Ethernet cable), and log in via SSH.

We will now setup the network on the RPI
The simplest way is using the 4th method: the RPI console(terminal) via the FTDI USB cable and minicom, because we can use the real board and `raspi-config`.

!!! Note
    Windows and Mac cannot natively access the filesystem partition of the Raspberry Pi image on the SD card because its format is `ext4`, the native Linux filesystem format. As a result, the easiest solutions are to access a computer with a Linux operating system or to use the 4th method (which is our case now).  


## Configure the network interfaces

We are still connected to the RPI board via serial

### Enable Wifi

To list all the network interfaces, we use the `ifconfig` command.

There should be 3 interfaces :

- lo: local interface (no physical network)
- eth0: ethernet RJ45 wired network
- wlan0: Wi-Fi wireless (radio) network

If wlan0 is not listed, as it is the case on a fresh install, you must specify the country where you use your device. This allows your device to choose the correct frequency bands for 5 GHz networking.  
We need to set the Wi-Fi country to FR.

Use raspi-config to set WLAN country to France

### Configure a Wifi interface

Since Raspberry Pi OS Bookworm (2023), the network is managed by NetworkManager, while on previous versions it was managed by dhcpcd.

!!! Note
    On versions older than Bookworm, there are 2 files to modify :

    - `/etc/wpa_supplicant/wpa_supplicant.conf` : to set the Wi-Fi
    - `/etc/dhcpcd.conf` : to set the IP addresses.

    Follow this tutorial if you need to do so :  
    [https://learn.sparkfun.com/tutorials/headless-raspberry-pi-setup](https://learn.sparkfun.com/tutorials/headless-raspberry-pi-setup)


A static IP address wil be use to simplify the access to the RPI via ssh.
Both ethernet and Wi-Fi interfaces will be defined, each with a
different IP address.

!!! Note
    For each network interface, a file will be created in `/etc/NetworkManager/system-connections`

We configure the network with NetworkManager using the `nmcli` (Network Manager Command Line Interface) command tool.

!!! Note "1 - Issue"

    Configure the Wifi connection to the **iot** (password: enstaL@b) network using `nmcli`
    Help on [https://www.raspberrypi.com/documentation/computers/configuration.html#wireless-networking-command-line](https://www.raspberrypi.com/documentation/computers/configuration.html#wireless-networking-command-line) 

!!! Tip "1 - Solution"

    ```bash
    sudo nmcli --ask dev wifi connect iot
    ```
    Enter password

Now check a file has been created for your wifi interface in `/etc/NetworkManager/system-connections`

```bash
ls /etc/NetworkManager/system-connections
```

Have a look to the created file. It should look like this :
```bash
[connection]
id=iot
uuid=74a90378-23c2-493e-a524-138a17c10184
type=wifi
interface-name=wlan0

[wifi]
mode=infrastructure
ssid=iot

[wifi-security]
auth-alg=open
key-mgmt=wpa-psk
psk=enstaL@b

[ipv4]
method=auto

[ipv6]
addr-gen-mode=default
method=auto

[proxy]

```

!!! Tip
    If we can't access the real board and we want to setup the network on the Raspberry Pi OS image from our computer,
    we would have to create and configure this file manually because `nmcli` doesn't work with chroot.

It is possible to encrypt the password using the `wpa_passphrase` tool for security reasons but this is optional.

```bash
wpa_passphrase <network_ssid> <password>
```

This will return an encrypted password. Copy it in your wifi interface configuration file instead of the real password

The iot network has internet connection, so you can try `sudo apt update` for example.

### Configure an ethernet interface

We may want to use a ethernet cable to connect to the RPI (for example to get a higher data rate than via Wifi), in this case
we can set eth0.

First, find the ethernet connection name (probably something like `Wired connection 1` or `eth0`):
```bash
nmcli connection show
```

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.10/24 \
  ipv4.gateway "192.168.1.1" \
  ipv4.dns "172.0.0.35 172.0.0.37 8.8.8.8 8.8.4.4"
```

Apply the change :

```bash
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

## Trying to find the IP address of the RPI

### From the host via network

In this case, suppose we don't have a FTDI USB cable and we configured the network interfaces on the RPI OS image on host

We need to proceed to a systematic search over all the network of your
hostname (the name you have given earlier in Lab 1).

!!! Note
    Can take a very long time with 16 bits masks. Faster, around one minute, with 24 bits masks.

On host, we can scan all devices on the iot network, this takes quite a long time (1 minute or more).
The iot network's ip address is 172.19.144.0  
Be carefull, on iot the network mask is 255.255.252.0 or /22

!!! Note "2 - Issue"
    Scan the iot network using `nmap`.
    Do this before and after booting up the raspberry pi, and find its ip address. 

!!! Tip
    Find the correct option to do a **Nmap Ping Scan**, which is a lot quicker than using nmap without option.

!!! Tip "2 - Solution"

    ```bash
    nmap -sP 172.19.144.0/22
    ```

### With the console (via minicom)

!!! Note
    This second option is easier but we need a serial connection to the real board, so more hardware

Simply show network configuration using
```bash
ifconfig
```

and look for inet.



## Enable ssh

The RPI image (even the lite one) has all softwares installed to
access it via ssh. However, port 22 is not accessible by default !

Try from host :
```bash
ssh ue41@<ip_addr>
```

The permission is denied !

Enable ssh on the RPI board using :
```bash
sudo systemctl enable ssh
```

Then start the service :
```bash
sudo systemctl start ssh
```

Check it is working :

```bash
systemctl status ssh

```

!!! Note
    We can also do it with `raspi-config`

!!! Tip
    When preparing the OS image on host or on the sd-card we would simply create a ssh file
    in the boot folder. When booting the real board with this image, a first-boot service checks for /boot/ssh,
    and enable ssh if it finds it. **It only works on first boot !**

    If you have already booted the board once with your image on your sd-card but you haven't created the /boot/ssh file before and you can't
    access the real board via serial, this method won't work.  
    Instead :  

    - Either use chroot on the sd-card and type :

    ```bash
    sudo systemctl enable ssh
    ```

    - Either create the /boot/ssh file on your local OS image and write it again on the sd-card

Now try again :
```bash
ssh ue41@<ip_addr>
```

You should now be able to connect via ssh ! You finished the setup !

**Congrats ! You ended this lab series !**  

You will soon use your knowledge to configure an embedded Linux for a robot.