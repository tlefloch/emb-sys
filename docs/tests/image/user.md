# E. User configuration

Let’s prepare access to the robot before its first boot.

## 1. Create a new user

Check if a default user already exists:

```bash
ls /home
```

f this command returns nothing, there is no default user.

!!! Note "Question 1"
    Create a new user with a name of your choice and set a password.
    You may leave the optional user information fields empty.

!!! Tip
    Make sure you remember the password you choose.
    You may use a simple password for the lab, but never do this on a real robot connected to a network.

!!! Tip
    You can change a user’s password later using: `passwd`

## 2. Give sudo privileges

On Ubuntu, you can grant sudo privileges to your user with:

```bash
usermod -aG sudo <username>
```

## 3. Add user to hardware-related groups

Our robot is a depth-controlled float designed to operate in open water for extended periods. At the end of its mission, the robot surfaces, acquires a GNSS signal, and transmits its position over radio so it can be recovered.

The GNSS module is connected to a serial port on the Raspberry Pi. To allow your user to read data from this serial port, the user must belong to the appropriate system group.

!!! Note "Question 2"
    Add your newly created user to the `dialout` group.

!!! Tip
    The command is very similar to the one used for adding a user to the `sudo` group.