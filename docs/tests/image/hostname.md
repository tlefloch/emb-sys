# D. System Identity Configuration

## 1. Set the hostname

The hostname defines how the robot is identified on the network.

!!! Note "Question 1"
    Choose a meaningful hostname and replace the default one in `/etc/hostname`.  
    What is the default hostname?

!!! Tip "Answer 1"
    The default hostname is **ubuntu**  
    Let's modify `/etc/hostname` with **seabot** instead of **ubuntu**

`/etc/hostname` defines the system’s hostname, but many programs also rely on `/etc/hosts` for local name resolution.

Edit `/etc/hosts` and make sure the line containing `127.0.1.1` matches your hostname.  
If no such line exists, add one below the `127.0.0.1   localhost` line.

---

## 2. Set the timezone

Robots often operate using UTC time, but you may want to set the timezone to match the robot’s geographic deployment area.

!!! Note "Question 2"
    Use:
    ```bash
    dpkg-reconfigure tzdata
    ```    
    Select the correct geographic area and city.  
    Include a copy of the output in your answers.

!!! Tip "Answer 2"
    Output :

    ```bash
    Current default time zone: 'Europe/Paris'
    Local time is now:      Mon Feb  9 10:42:02 CET 2026.
    Universal Time is now:  Mon Feb  9 09:42:02 UTC 2026.
    ```
