# wch32_macos

## Preparing Toolchains
-   Download MRS toolchain from MacOS tab at http://www.mounriver.com/download.
-   Download Wlink for arm64 from https://github.com/ch32-rs/wlink/releases/tag/nightly.
-   Install required tools with homebrew by 
    
        brew install libusb
-   Extract MRS toolchain and Wlink into same directory.

![](https://github.com/ozturkhuseyin/wch32_macos/blob/main/etc/dr.png "Toolchain directory")

## First example on CH32V003 board
-   Clone this repository to your computer

        git clone https://github.com/ozturkhuseyin/wch32_macos.git

-   Change toolchain path for yourself in CH32V003F4P6_template project
        
        vim CH32V003F4P6_template/Makefile

-   Then change the first line of Makefile

        WCH_RV_TOOLCHAIN_DIR ?= /path/to/toolchain/directory

-   Build CH32V003F4P6_template project

        cd CH32V003F4P6_template
        make all
-   Connet WCH-Link to your board then your computer

        make flash_erase
        make flash_program
        make reset

    or just

        make upload_program


## TODO
-   Add template projects for other development boards