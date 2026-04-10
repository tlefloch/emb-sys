# B. Configure Docker for multiplatform images

## containerd

Let's make sure our Docker setup is ready for use of multiplatform images.

Multi-platform images require the **containerd** image store. Docker Desktop and Docker Engine 29.0+ use the **containerd** image store by default, which supports multi-platform images out of the box. If you're using one of these versions, no additional setup is needed.

Check your docker version.

If it is lower than 29.0, update Docker Engine or follow instructions on [https://docs.docker.com/engine/storage/containerd/](https://docs.docker.com/engine/storage/containerd/) to enable containerd.

## QEMU

In this lab, we will use emulation via **QEMU** to build and run multiplatform images.  

QEMU is the easiest way to get started for multiplatform building. It requires no changes to your Dockerfile and the secondary architectures that are available are automatically detected.


QEMU is included with Docker Desktop.  
If you're using a builder outside of Docker Desktop, such as if you're using Docker Engine on Linux, or a custom remote builder, you need to install QEMU

Follow instructions on [https://docs.docker.com/build/building/multi-platform/#qemu](https://docs.docker.com/build/building/multi-platform/#qemu) to setup QEMU.



Learn more about **Multiplatform Building** on : [https://docs.docker.com/build/building/multi-platform/](https://docs.docker.com/build/building/multi-platform/)