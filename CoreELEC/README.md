# Build CoreELEC in a docker container on CoreELEC

These instructions are for creating a Docker container and building a CoreELEC image on a device running CoreELEC.
If you are using another operating system, you should use the [README.md](../README.md) one directory level up.

* It is highly recommended to avoid building on a micro SD card, please use an external SSD/HDD or internal eMMC.  
You may need to modify the paths used as examples in this guide (`~/ce-builder`, `/storage/.swapfile`) to suit your particular setup.
* A full build requires 40GB of free space. If you want to build add-ons, an additional 20GB of free space is required.

## Prerequisites

* Install the `Services - Docker` add-on in Kodi, reboot.

Log in to CoreELEC via SSH and follow the rest of the guide.

* Stop Kodi to free up resources: `systemctl stop kodi`

## Create swap file

You may need to create a swap file in order to avoid the system from locking up during the build process.
On S922X you will need a total of 6GB of memory, meaning a 2GB swap if your device has 4GB of RAM and 4GB swap if it has 2GB of RAM.  
On S905* devices, you will need 4GB of memory, meaning no swap is necessary if your device has 4GB of RAM and 2GB swap is required if it has 2GB of RAM.  
Run the following commands to create and activate a 2GB swap file. Change the `count` value to adjust the swap file size.

```sh
dd if=/dev/zero of=/storage/.swapfile bs=1M count=2000
chmod 600 /storage/.swapfile
mkswap /storage/.swapfile
swapon /storage/.swapfile
```

You can place the following code in `/storage/.config/autosart.sh` to make the swap file persist across reboots.

```sh
#!/bin/bash

if [ ! -f /storage/.swapfile ]; then
    dd if=/dev/zero of=/storage/.swapfile bs=1M count=2000
    chmod 600 /storage/.swapfile
fi
mkswap /storage/.swapfile
swapon /storage/.swapfile
```

## Prepare your environment

* If you are using an external (USB) drive, you need to mount it with execute permissions:  
`mount -o remount,exec /path-to-external-drive-partition`
* `mkdir -p ~/ce-builder`
* `chown -R 1000 ~/ce-builder`

## Pull the image and run the container

* `docker pull coreelec/coreelec-builder:latest-aarch64`
* `docker run -v ~/ce-builder:/home/docker -h coreelec -it coreelec/coreelec-builder:latest-aarch64`

## Clone repo and build image inside container

* `git clone git://github.com/CoreELEC/CoreELEC.git ~/CoreELEC`
* `cd ~/CoreELEC`
* `time(PROJECT=Amlogic-ng ARCH=arm make image)`  
Use `PROJECT=Amlogic` to build images for older S912 and S905/X/D devices
