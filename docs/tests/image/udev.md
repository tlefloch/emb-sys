# H. Udev Rules — Hardware Access

## 1. What are **udev rules**?

`udev` is the device manager for Linux. It controls:

- How hardware devices appear in `/dev`  
- Who can access them  
- What permissions are set when a device is plugged in  

On a robot, many sensors are connected via USB, serial, or other interfaces. By default, you often need root access to read/write to these devices.

Using udev rules, you can:

- Assign **permissions** to a device for a specific user or group  
- Give the robot software **automatic access** to hardware without needing `sudo`  
- Name devices consistently, e.g., `/dev/gnss0` or `/dev/hydrophone`  

This is essential for embedded robotics: the robot must access its sensors automatically at boot, without manual intervention.

---

## 2. Create a udev rule

In order to avoid hitting the seafloor when regulating its depth, the Seabot is equipped with a [Ping Sonar Altimeter and Echosounder](https://bluerobotics.com/store/sonars/echosounders/ping-sonar-r2-rp/) from Blue Robotics. We will create a udev rule for it.

### Step 1: Identify the device

If you had a real device, you would first plug it in and find it with:

```bash
ls /dev/tty*
```

Let's assume the device appears as /dev/ttyUSB0.

Then check its vendor and product IDs (won’t work here since we don’t have the device):

```bash
udevadm info -a -n /dev/ttyUSB0
```

We will look for lines like :
```bash
ATTRS{idVendor}=="1234"
ATTRS{idProduct}=="5678"
```

For this lab, assume we got the following IDs:

- **Vendor ID** : 4896
- **Product ID** : 7912

### Step 2: Create the rule file

udev rules files are stored in `/etc/udev/rules.d/`

!!! Note "Question 1"
    Create a `99-seabot.rules` udev file

### Step 3: Add a rule

Every time the Raspberry Pi detects a device with the echosounder’s vendor and product IDs, it will:
- Give users in the dialout group access to it
- Give read/write permissions for everyone
- Create a symlink named **ping1D** pointing to the device

Add the following line in the udev file:
```bash
SUBSYSTEM=="tty", ATTRS{idVendor}=="<vendor_id>", ATTRS{idProduct}=="<product_id>", SYMLINK+="<new_device_name>", GROUP="dialout", MODE="0666"
```

| Field              | Meaning                             |
| ------------------ | ----------------------------------- |
| `SUBSYSTEM=="tty"` | Apply to serial/USB devices         |
| `ATTRS{idVendor}`  | Vendor ID of the device             |
| `ATTRS{idProduct}` | Product ID of the device            |
| `SYMLINK+=        `| Symlink name to the device          |
| `GROUP="dialout"`  | Users in `dialout` group can access |
| `MODE="0666"`      | Read/write permissions for everyone |


!!! Note "Question 2"
    Edit the udev file to create the rule for the echosounder.

    Show your line in your answers.

