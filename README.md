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

## Using Pre-Build Images

1. Extract the 7zip archive.

```bash
7z x rpi-basic-raspberrypi3.rpi-sdimg.7z
```

2. Copy the image to the SD Card. Immediately after inserting the SD Card, discover your devices name viewing hte dmesg log.

```bash
dmesg | tail
```

In my case this was `sdc` as seen below.

```
[177703.081102] sd 2:0:0:0: [sdc] 60579840 512-byte logical blocks: (31.0 GB/28.9 GiB)
[177703.081967] sd 2:0:0:0: [sdc] Write Protect is off
[177703.081972] sd 2:0:0:0: [sdc] Mode Sense: 03 00 00 00
[177703.082854] sd 2:0:0:0: [sdc] No Caching mode page found
[177703.082863] sd 2:0:0:0: [sdc] Assuming drive cache: write through
[177703.088180]  sdc: sdc1 sdc2
[177703.091663] sd 2:0:0:0: [sdc] Attached SCSI removable disk
[177703.537707] EXT4-fs (sdc2): mounting ext3 file system using the ext4 subsystem
[177703.812323] EXT4-fs (sdc2): recovery complete
[177703.816525] EXT4-fs (sdc2): mounted filesystem with ordered data mode. Opts: (null)
```

```
dd if=rpi-basic-raspberrypi3-20190828023638.rootfs.rpi-sdimg of=/dev/sdx bs=4M
sync
```

3. Extend the Root Partition of the SD Card Image to match your SD card image size.

```bash
growpart /dev/sdc 2
resize2fs /dev/sdc2
```

# Using a Built Image

Once you have built your SD card image, you need to burn it to that SD card.

1. Find your SD card. If you have just plugged it in, the easiest way is to view the logs.

```bash
dmesg | tail
```

or view the different disks on your system.

```bash
fdisk -l
```

2. Use dd (disk destroyer) to copy your image over. I am assuming you have your project in the default location. Replace `/dev/sdx` with the appropriate disk. Ex `/dev/sdb`.

```
cd ~/Documents/poky/build
dd if=tmp/deploy/images/raspberrypi3/rpi-basic-raspberrypi3.rpi-sdimage of=/dev/sdx bs=4M
sync
```
