# I. Systemd service

When operating on field, we don't want to loose time with the whole process of connecting to the robot and launching the programs.  
To avoid it, we want the robot to launch its program automatically at boot.

For this lab we will launch a simple python script, but with ROS2 you could launch all your nodes at boot !

## 1. Create a simple python script

!!! Question 1
    Create a python script named `boot_script.py` in the user’s home directory, that simply prints "Seabot operating" every 2s.  
    Execute it with `python3` to check it works.

## 2. Create a systemd service file

`systemd` is the init system in modern Linux. It manages:

- Starting and stopping services at boot
- Dependencies between services
- Logging and restarting on failure

A systemd service is basically a unit file that tells Linux:

- What program to run
- When to run it
- Under which user
- Restart behavior if it fails

### Step 1: make the script executable

Here my user is `pi`, replace by yours

```bash
chmod +x /home/pi/boot_script.py
```

### Step 2: Create the service file

Systemd service files go in `/etc/systemd/system/`

!!! Note "Question 2"
    Create a `boot_script.service` file in `/etc/systemd/system/`

Put this content in the file :

```bash
[Unit]
Description=Boot Python Script

[Service]
Type=simple
ExecStart=/usr/bin/python3 /home/pi/boot_script.py
WorkingDirectory=/home/pi
User=pi
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Explanation of fields :

| Field                        | Meaning                                   |
| ---------------------------- | ----------------------------------------- |
| `Description`                | Human-readable description of the service |
| `Type=simple`                | Standard service that runs continuously   |
| `ExecStart`                  | Command to execute                        |
| `WorkingDirectory`           | Directory from which the script runs      |
| `User`                       | Runs as this user instead of root         |
| `Restart=on-failure`         | Automatically restarts if it crashes      |
| `WantedBy=multi-user.target` | Starts at normal multi-user boot          |

### Step 3: Enable and start the service

```bash
sudo systemctl daemon-reload         # reload systemd to detect new service
sudo systemctl enable boot_script    # enable at boot
sudo systemctl start boot_script     # start now
```

The start command won't work as we are in chroot but the service will start when booting on a reak board.
