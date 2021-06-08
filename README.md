# raspi-bake

Headless configuration of a Raspberry Pi image, device or mount point

## Installation

raspi-bake requires the following dependencies installed in your system:

- `systemd-container` to run the Raspberry Pi container
- `qemu-user-static` for ARM emulation
- `parted` for partition management
- `unzip` if downloading the official base image is required

To install in Ubuntu, issue the following command:

```
sudo apt-get install parted qemu-user-static systemd-container unzip
```

## Usage

Some examples of the basic usage of this tool are:

```
raspi-bake rpi.img
raspi-bake /dev/mmcblk0
raspi-bake -d /mnt/boot /mnt/rootfs
```

which will start a shell in the emulated container. Additional options can be used to ensure a minimum image size or run inline scripts; use `raspi-bake -h` to show all the options.

When a regular file is specified, a new base image is downloaded from the official site if the file does not exist, and it is made sparse on exit to minimize disk utilization.

The root partition of the host system is mounted under `/mnt/host`, and the working directory inside the container is automatically set to the current working directory inside this folder. This means that relative paths work exactly the same as they would in the host.

## Limitations

The underlying container is run with just a [minimal stub init process](https://www.freedesktop.org/software/systemd/man/systemd-nspawn.html#-a), which means that any command relying on a full init system will not work. However, in most cases this is only needed to avoid system restarts, something that is not necessary in a headless installation. If you want to avoid showing errors in these cases, you can check for the existence of the init process in your installation script:

```
pidof -q systemd && systemctl restart <service>
```

Several utilities are included in the `bin` folder, similar to the ones available in `raspi-config`, to provide some basic functionality without relying on an init system, avoiding commands such as `systemctl` and `wpa_cli`. A more advanced example is shown below to resize the image to 2 GB and enable SSH access:

```
raspi-bake -r 2G rpi.img -P < raspi-bake/bin/ssh
```
