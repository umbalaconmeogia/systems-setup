# Install applications on Ubuntu 26.04

## Overview

Install applications after installing Ubuntu 26.04.

## Common installation for both Ubuntu Desktop and Ubuntu Server

### System update and upgrade

```sh
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
```

## Installation for Ubuntu Desktop

### Language setting

#### Change language to English

* Add language 設定 > システム > 地域と言語 > 言語サポート dialog appears. Add 英語
* Change 言語 to English (United States).

#### Install Unikey

```shell
sudo apt-get install ibus-unikey
ibus restart
```

### Install Google Chrome

```sh
# 1. Download Chrome .deb package
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
# 2. Install the package
https://linuxconfig.org/how-to-install-google-chrome-on-ubuntu-26-04
# 3. Launch Chrome
google-chrome
# 4. Remove .deb package
rm google-chrome-stable_current_amd64.deb
```

References:
* https://linuxconfig.org/how-to-install-google-chrome-on-ubuntu-26-04
