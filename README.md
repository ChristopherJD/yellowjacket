# Yellojacket

A Yocto based Raspberrypi OS

## Pre-Built Images and SDK

**Downlaod the SDK**

You can download the pre-built SDK [here](https://mega.nz/#!sWBSGILZ!kblHVvsRGVXX5L40IQOSyjV4uaMQgOCf5kPsEy2EkNE).

**Download the Raspberry Pi SD Card Image**

You can downlaod the pre-build image [here](https://mega.nz/#!ZfZy1C4D!j740Z5DGhR1bp93Z1k2HShKwzVuNfJBgBlOq_83QFaw)

## Dependencies Installation

Depency installation may differ depending on your distro, below is the requirements running Manjaro .

```sh
sudo pacman -Syu chrpath cpio diffstat rpcsvc-proto
```

### Setup

(Optional) However it is recommended that you change the location that yocto uses for package download. I have mounted an external hard drive to store these packages.

**Adding an External Hard Drive**

The following steps will cause your hard drive to be mounted automatically at startup.

1. Create a directory to mount the hard drive.

    ```bash
    sudo mkdir /media/external_500
    sudo chown -R $(id -u):$(id -u) /media/external_500/
    ```

1. Check the UUID of the hard drive to add to the fstab file.

    ```bash
    sudo blkid
    ```

1. Append the device to the `/etc/fstab` file. Please note that you should replace your UUID with the one found in the above command. Below is only to be used as reference.

    ```bash
    UUID=bc3040c9-ae5a-4ffc-93ab-466ca2444616 /media/external_500 ext4 rw,auto,nofail 0 0
    ```
  
**Change the Configuration**
  
1. Change into the build configuration directory.

    ```bash
    cd poky/build
    ```
  
1.  Edit the `conf/local.conf` file.

    Old:

    ```
    DL_DIR ?= "/media/external_500/downloads"
    SSTATE_DIR ?= "/media/external_500/sstate-cache"
    ```
  
    New:

    ```
    DL_DIR ?= "${TOPDIR}/downloads"
    SSTATE_DIR ?= "${TOPDIR}/sstate-cache"
    ```

# Build

## Setting up the environment

```sh
git submodule update --init --recursive
source poky/oe-init-build-env
```

## Build Raspberry Pi OS

```sh
bitbake rpi-basic
```

## Build Raspberry Pi SDK

```sh
bitbake rpi-basic -c do_populate_sdk
```
