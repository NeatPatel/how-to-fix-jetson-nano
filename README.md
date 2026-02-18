How to factory reset and boot from USB and enable fan for NVIDIA Jetson Nano 16GB eMMC module:

Required tools checklist:
- Host PC that has Ubuntu 18.04 (exactly this version, it is called Bionic-Beaver -- USB flash version works)
- Certain Required packages (in the "Packages" folder)
- Working internet/ethernet on host PC
- Recommended: approx. 16 GB external USB drive empty (format this to EXT4 format)
- Optional: minimum 64 GB microSD card

The USB drive is REQUIRED if you are using USB booted Ubuntu 18.04.

1) Install all required packages

// Run these commands

sudo bash -c 'cat > /etc/apt/sources.list <<EOF

deb http://archive.ubuntu.com/ubuntu/ bionic main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu/ bionic-updates main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu/ bionic-backports main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu bionic-security main restricted universe multiverse
EOF'

sudo apt update

sudo apt install abootimg lbzip2 qemu-user-static sshpass liblz4-tool

sud apt-get install libxml2-utils

Afterwards, go into a preferred directory in your 16 GB USB drive (or, if you have room on your Ubuntu PC, somewhere in there), and run:

// Make a workspace

mkdir workspace

cd workspace

// Install 2/3 packages via wget

wget https://developer.nvidia.com/downloads/embedded/l4t/r32_release_v7.6/t210/jetson-210_linux_r32.7.6_aarch64.tbz2

wget https://developer.nvidia.com/downloads/embedded/l4t/r32_release_v7.6/t210/tegra_linux_sample-root-filesystem_r32.7.6_aarch64.tbz2

**AT THIS POINT, COPY OVER THE "206_nano_jp466.tbz2" PACKAGE FROM YOUR USB INTO WORKSPACE DIRECTORY

2) Unzip your files and get ready to flash

// This one takes a second, unzipping

tar xf jetson-210_linux_r32.7.6_aarch64.tbz2

// This one takes a second, unzipping

sudo tar xpf tegra_linux_sample-root-filesystem_r32.7.6_aarch64.tbz2 -C Linux_for_Tegra/rootfs/

cp 206_nano_jp466.tbz2 ./Linux_for_Tegra

cd Linux_for_Tegra/

// This one takes a second, applying binaries

sudo ./apply_binaries.sh

// This one takes a second, unzipping the extra package (CRUCIAL STEP)

sudo tar xf 206_nano_jp466.tbz2

3) Flash your board

B01 SUB must enter Recovery mode to perform flashing. Follow these steps:

(1). Short the REC pin and GND pin on the carrier board.

(2). Connect the carrier board to your computer using a Micro-USB cable.

(3). Power on the device.

(4). Run lsusb on your computer. If the product ID is (7f21), the device has entered Recovery mode.

then run:

sudo ./flash.sh jetson-nano-emmc mmcblk0p1

^ if any package errors, please find and download the required packages

4) Connect and boot up!

Connect your board to power (NO OTHER CABLES CONNECTED) and then after a few seconds plug in HDMI. Your board should boot up now!

5) Boot from SD card (make sure you have minimum 64 GB micro-SD card first)

**THIS IS WITHIN THE JETSON, NOT HOST PC, BOOT ON JETSON BEFORE THIS

Plug in your microSD card into the SD card slot

Follow the instructions at the following link (section name: "TF Card Boot / USB Disk Boot"):
https://wiki.youyeetoo.com/en/JETSON_NANO/Firmwareupdate

**NOTE: when booting from sd instructions, at the very end when you modify the extlinux.conf file, do the following:

- from base directory (very top directory), go to /boot/extlinux
- if you see 2 files, edit them BOTH correspondingly to the instructions (make sure to maintain each file's respective structure)
- after completing, run "sudo reboot", and now you should boot from microSD (the "External SD" should now be 16 GB instead of your SD)

6) Enable fan (optional)

RUN THIS FROM WITHIN THE JETSON, NOT HOST PC

git clone https://github.com/Pyrestone/jetson-fan-ctl.git

// Run this to go back to very top directory (just "cd")

cd

sudo ./install.sh

Visit this link for customization:

https://github.com/Pyrestone/jetson-fan-ctl
