# Introduction to QEMU

## Introduction

**QEMU**, which stands for **Quick Emulator**, is an open-source
software project that provides an emulation and virtualization
framework. It is designed to run a variety of guest operating
systems on a host operating system, and can emulate a wide range
of hardware platforms.
**QEMU** has been created by Fabrice Bellard (also known for
ffmpeg)  

**QEMU** can be used for a variety of purposes, including:

- Running legacy operating systems or software on modern
hardware.
- Running multiple operating systems on a single host machine
for testing or development purposes.
- Providing a safe and isolated environment for running
untrusted or potentially malicious code.
- Developing and testing software for embedded devices or
other specialized hardware platforms.

Visit [QEMU website](https://www.qemu.org/) to learn more

## How QEMU works

**QEMU** works by emulating the hardware components of a
computer, including the CPU, memory, storage devices, network
adapters, and input/output devices.

When a guest operating system is started, it runs on top of the
emulated hardware, just as it would on a physical machine. The
guest operating system sees the emulated hardware as if it were
real, and interacts with it accordingly.

**QEMU** can also provide virtualization capabilities, allowing
multiple operating systems to run on a single host machine with
minimal performance overhead. In this mode, **QEMU** uses
hardware virtualization extensions (such as Intel VT-x or AMD-V)
to provide isolated environments for each guest operating system.

## Using QEMU

**QEMU** is a powerful and versatile tool that can be used for a
variety of purposes, from running legacy software to testing new
operating systems. With its ease of use and wide range of features,
**QEMU** is a must-have tool for any system administrator or
developer.

## Install

```bash
sudo apt-get install qemu-system qemu-user-static binfmt-support
```