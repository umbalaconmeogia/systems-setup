[English](README.md) | [Tiếng Việt](README.vi.md) | [日本語](README.ja.md)

# Install Ubuntu 26.04

## Overview

This document is a note about installing Ubuntu 26.04 (both Desktop and Server version) OS and applications on a local PC.

* Install OS with hard disk encrypted.
* Install applications and set up.

## Install OS with hard disk encrypted

Install Ubuntu automatically using **autoinstall.yaml**.

* Using autoinstall.yaml is a difficult experience. I ended up with a minimal configuration.
* Autoinstall on Ubuntu 26.04 is different from on Ubuntu 24.04.
  * On Ubuntu 26.04, just put the file autoinstall.yaml into the installer USB, and the Ubuntu installer will automatically find it and ask whether you want to use it (you can add autoinstall boot option for it running automatically).
  * On Ubuntu 24.04, you should create the file *user-data* (same as autoinstall.yaml) and an empty file *meta-data*, put them in the USB, and when booting from the USB, add the *autoinstall ds=nocloud;s=/cdrom/* option.

In the example [autoinstall.yaml](autoinstall.yaml), we create 3 partitions and leave a space unallocated.
| No. | Mount       | Size   | Description                                                                                                                                                   |
| --- | ----------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | /boot/efi   | 1 GB   | Install GRUB                                                                                                                                                  |
| 2   | /           | 200 GB | Install Ubuntu root                                                                                                                                           |
| 3   | /home       | 630 GB | Store /home, and is encrypted with LUKS                                                                                                                       |
| 4   | unallocated | 100 GB | We leave 100 GB unallocated for [SSD garbage collection](https://umbalaconmeogia.wordpress.com/2026/05/05/tai-sao-nen-chua-ra-10-unallocated-space-tren-ssd/) |

To use this autoinstall.yaml, you must set 3 values:
1. SSD serial number (to help the installer to find exact device to format).
2. Password to encrypt partition mount to /home.
3. Hashed password for the Ubuntu user. To create the hash, use the command `openssl passwd -6 'your_password_here'`

## Install applications and setup

Refer to [SetupUbuntu26.04.md](SetupUbuntu26.04.md) for notes on setting up the Ubuntu 26.04 environment.