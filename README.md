# linstaller
A minimalistic universal easy-to-implement linux installer

## Why?

Current Linux installers ended up absorbing out-of-scope functions, which made them complex and difficult to debug, they also eventually adopted the logic of formatting, extracting the image, configuring and only then installing the boot loader, meanwhile allows a reliable installation, this can result in an incomplete installation if for some reason some configuration such as generating locales, or cleaning the package manager fails, this results in an unbootable system, `linstaller` tries to maintain reliability however focusing on providing an uncivilized system instead of a pre-configured one, with the exception of the user and the hostname, all configurations are done by a post-installation script, ensuring that the system in minimal conditions can boot after installation

## `listaller` setup

Configura `listaller` é incrivelmente simples:

- [`linstaller-backend`](https://raw.githubusercontent.com/natanael-b/linstaller/main/linstaller-backend)is the installer, put it in the image
  - And these are the dependencies:
    - `grub-install`
    - `grub-pc`
    - `grub-efi`
- [`linstaller-fulldisk-setup`](https://raw.githubusercontent.com/natanael-b/linstaller/main/linstaller-fulldisk-setup)  is the formatter, it is the simplest way to configure a blank disk
  - And these are the dependencies:
    - `gdisk`
    - `mkfs.fat`

On a Ubuntu/Debian based system this install everything you need:

```bash
sudo apt install gdisk                  \
                 squashfs-tools         \
                 grub-common            \
                 grub-pc-bin grub-efi   \
                 grub-efi-amd64-signed  \
                 dosfstools             # Run on image
```

## Disk setup

You will need to setup a disk, `linstaller` don't do this but provide a utility to quick setup a full-disk installation, once time installed `linstaller` on `/usr/bin` you shold be able (as root) to:

```bash 
linstaller-fulldisk-setup --filesystem=ext4 /dev/sda
```

This will **erase all partitions and data** on `/dev/sda` create a 512M partition for EFI as FAT32 (even on MBR), create a 2nd partition with all remaining disk size and format as `ext4`

> **Tip:** You can format system target partition with any of `mkfs` supported format, you can list the supported with `ls /usr/sbin | grep "mkfs\." | cut -d\. -f2`

## Usage

The script supports various arguments that specify installation settings. Here are the supported arguments:

* `--target-partition=<target_partition>`: Specifies the target partition where the system will be installed.`*`
* `--home-partition=<home_partition>`: Specifies the user's home partition.
* `--user-name=<user_name>`: Specifies the name of the user to be created during installation.`*`
* `--user-password=<user_password>`: Specifies the user's password.`*`
* `--squashfs=<squashfs_file>`: Specifies the SquashFS file containing the system image to be installed.`*`
* `--hostname=<host_name>`: Specifies the hostname for the system.`*`
* `--timezone=<timezone>`: Specifies the system's timezone.`*`
* `--keyboard-map=<keyboard_layout>`: Specifies the keyboard layout.`*`
* `--target-disk=<target_disk>`: Specifies the target disk where Grub will be installed.`*`
* `--efi-partition=<efi_partition>`: Specifies the EFI partition (required if the system is being installed on a UEFI system).`**`
* `--language=<language>`: Specifies the system language.`*`

> **Notes:**
> * `*` Mandatory
> * `**` Mandatory if the system is booted in UEFI mode.
> * See [linstaller-example](linstaller-example) for usage example

## How does it work?
The script performs the following steps during installation:

1. Mounts the target partition at /target in read-write mode.
2. Mounts the home partition (if specified) at /target/home.
3. Extracts the system image from the specified SquashFS file to /target.
4. Generates the /target/etc/fstab file for automatic partition mounting.
5. Installs Grub on the destination disk (considering EFI or not).
6. Configures language, keyboard layout, time zone, hostname, and creates a user with the specified password.
7. Executes any custom post-installation script, if provided in the image.

## The post install

Since `linstaller` is designed to install the system in the most fail-safe way possible, all configuration should be done through a post-installation script/software that should be located in the squashfs image at `/usr/bin/post-install`. Any arguments not supported by linstaller are passed to this script/software for further processing.
