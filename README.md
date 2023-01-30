# Environment Setup for Matter Development - Complete Instructions for Ubuntu 22.04

# Distro Installation
Open a powershell window in administrator mode
Install WSL and Ubuntu simultaneously:

    wsl --install -d ubuntu


Install updates and prereqs

    sudo apt-get update
    sudo apt-get upgrade

IDF Preq's

    sudo apt-get install git wget flex bison gperf python3 python3-pip python3-setuptools cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0

Matter Preq's

    sudo apt-get install git gcc g++ pkg-config libssl-dev libdbus-1-dev \
        libglib2.0-dev libavahi-client-dev ninja-build python3-venv python3-dev \
        python3-pip unzip libgirepository1.0-dev libcairo2-dev libreadline-dev

# Setup COM Port Access
First need to check list of already installed wsl distros

    wsl --list

Set the distro you will be developing in as the default

    wsl --set-default <distro name>

Get the install for usbpid

    winget install --interactive --exact dorssel.usbipd-win

List the available ports to attach to the WSL VM

    usbipd list


Open terminal to WSL2 Ubuntu, and do the following

    sudo apt install linux-tools-5.4.0-77-generic hwdata
    sudo update-alternatives --install /usr/local/bin/usbip usbip /usr/lib/linux-tools/5.4.0-77-generic/usbip 20

Attach the desired usb port to the WSL VM

    usbipd wsl attach --busid <bus-id>

By default users do not have dialout access

    sudo usermod -a -G dialout <username>

Must logout and log back in

# IDF Setup
Clone ESP-IDF v4.4.3 release, move to desired development folder first!

    git clone -b v4.4.3 --recursive https://github.com/espressif/esp-idf.git
    cd esp-idf
    ./install.sh

# ZAP Setup
Pull latest release of ZAP, make sure to return back to development directory
https://github.com/project-chip/zap/releases/
    
    mkdir zap
    cd zap
    wget https://github.com/project-chip/zap/releases/download/<version>/zap-linux.zip
    unzip zap-linux.zip

Setup variables so code knows where ZAP is

    export ZAP_INSTALL_PATH=$ZAP_INSTALL_PATH/path/to/zap

# Matter Source Code
Pull latest connectedhomeip code, remember to return to development directory!

    git clone --recurse-submodules https://github.com/project-chip/connectedhomeip.git

# Building and Programming Matter
Go to where ESP-IDF is cloned in to and run export script

    cd /path/to/esp-idf
    source export.sh

Go to where connectedhomeip is cloned and run activate script

    cd /path/to/connectedhomeip
    source scripts/activate.sh

Enable Ccache for faster IDF builds

    export IDF_CCACHE_ENABLE=1

Go to path of project to build and program, then set target and build
    cd /path/to/project/esp32/
    idf.py set-target esp32s3
    idf.py build

Program device

    idf.py -p <port> erase-flash
    idf.py -p <port> flash monitor
