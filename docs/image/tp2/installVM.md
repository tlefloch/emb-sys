# B. Install QEMU ARM virtual machine (VM)

Install require packages for QEMU virtual machine (QEMU VM).

Update package manager
```bash
sudo apt-get update
```

Install packages for the QEMU VM
```bash
sudo apt-get install qemu-system-arm \
qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils \
virtinst libvirt-daemon virt-manager
```
If problems with lmv2 during install
```bash
sudo apt-get purge lvm2
sudo apt-get install lvm2
```
When running Raspberry PI OS in the QEMU VM, we need the Linux
kernel and drivers to be compatible with an execution on a QEMU VM
instead of the actual RPI board. They are available here :
https://github.com/dhruvvyas90/qemu-rpi-kernel

We assume that we are in the rpilab folder
clone the git repo with kernels and drivers
```bash
git clone https://github.com/dhruvvyas90/qemu-rpi-kernel
```
to avoid problems of git submodules, you can remove the .git folder
```bash
rm -Rf qemu-rpi-kernel/.git
```