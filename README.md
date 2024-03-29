# HP-EliteDesk-800-G2-DM-Hackintosh
This repository contains the files and scripts to install macOS on the HP EliteDesk 800 G2 Desktop Mini Business PC (35W/65W).

![HP EliteDesk 800 G2 Desktop Mini Business PC](https://ssl-product-images.www8-hp.com/digmedialib/prodimg/lowres/c04876268.png)

## Hardware Specs
- CPU: Intel(R) Core(TM) i5-6500 CPU @ 3.20GHZ
- GPU: Integrated Intel® HD Graphics 530 (2 DisplayPorts + 1 VGA Port)
- Product Name: HP EliteDesk 800 G2 DM 65W
- System BIOS: N21 Ver. 02.53 04/16/2021 and/or N21 Ver. 02.56 04/08/2022
- Memory: 8 GB Micron DDR4-2400
- LAN: Intel® I219M Gigabit Network Connection LOM
- Audio: Realtek ALC221 Audio (1 internal speaker + 1 front headphone + 1 front CITA port)


## BIOS Settings
- Advanced -> Boot Options
  - Disable **Fast Boot**
  - Disable **CD-ROM Boot**
  - Enable **USB Storage Boot**
  - Disable **Network (PXE) Boot**
  - UEFI & Legacy Boot order, is up to you.
  
- Advanced -> Secure Boot Configuration
  - Select **Legacy Support Enable and Secure Boot Disable**
  - Everything else unchecked.

- Advanced -> System Options
  - Enable **Turbo-boost**
  - Enable **Hyperthreading**
  - Enable **Multi-processor**
  - Enable **Virtualization Technology (VTx)**
  - Disable **Virtualization Technology for Directed I/O (VTd)**
  - Enable **M.2 WLAN/BT**
  - Enable **M.2 SSD**
  - Enable **Allow PCIe/PCI SERR# Interrupt**

- Advanced -> Built-in Device Options
  - Enable **Embedded LAN Controller**
  - Disable **Wake on LAN**
  - Video memory size **64MB**
  - Enable **M.2 USB / Bluetooth**
  - Enable **Audio Device**
  - Enable **Internal Speakers**

- Advanced -> Port Options
  - Everything **Enabled**
  - Restrict USB Devices **Allow all USB Devices**

- Advanced > Option ROM Launch Policy
  - Configure Option ROM Launch Policy **All UEFI**

- Advanced -> Power Management Options
  - Runtime Power Management **Enable**
  - Extended Idle Power States **Enable**
  - S5 Maximum Power Savings **Disable**
  - SATA Power Management **Enable**
  - PCI Express Power Managment **Disable** (if enabled, it will really screw that native BCM M.2 WiFi performance)
  - Power On from Keyboard Ports **Disable**
  - Unique Sleep State Blink Rates **Disable** 

- Advanced -> Remote Management Options
  - Enable **ATM** (without it Intel's ME won't be available and macOS will crash under some circunstances)
  

Press **F10** to save changes.

### Current OS
- macOS Monterey 12.4


### Bootloader / Kexts
- OpenCore v0.9.1
- AppleALC
- CPUFriend.kext
- IntelMausi.kext
- Lilu.kext
- RTCMemoryFixup.kext
- SMCProcessor.kext
- SMCSuperIO.kext
- USBPorts.kext
- VirtualSMC.kext
- WhateverGreen.kext

## How Install
Follow the instructions at https://dortania.github.io/OpenCore-Install-Guide/installer-guide/ in order to create your macOS installation media and then add the provided EFI folder in this repository.

## iServices (iMessage, iCloud etc) and FileVault

In order to have iServices you need to change a few keys in your `config.plist`:

- ApECID
- MLB
- ROM
- SystemSerialNumber
- SystemUUID

Please refer to the following guides to know what needs to be done:

- https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#using-gensmbios
- https://dortania.github.io/OpenCore-Post-Install/universal/security/filevault.html


## Known Issues
- VGA port - black screen
- Can't change the DP port I'm using after the machine is ON - results in a black screen
- Can't boot without a screen connected
- Sleep doesn't work. Black screen and system crash after trying to wake it up
- Front Headphone/Mic combo jack is not working, headphone port works, and the combo jack works as line-in

## About Sleep

Sleep not working due to issues with the Intel® HD530 iGPU. This GPU is known to be problematic and the Acidanthera team doesn't seem interested in it. A lot of time, tests and discussion about the iGPU issues happened here:

- https://www.tonymacx86.com/threads/opencore-sleep-hp-elitedesk-800-g2-mini-i5-6500.320697/
- https://github.com/acidanthera/bugtracker/issues/2044
- https://github.com/randyzhong/HP-EliteDesk-800-G2-DM-Hackintosh/issues/11
- https://github.com/randyzhong/HP-EliteDesk-800-G2-DM-Hackintosh/issues/5
- https://github.com/frostyviking/HP-EliteDesk-800-G2-OC/issues/2

Although sleep doesn't work properly we can always turn off the display after a few minutes. This will work and wake up properly as long as we 1) tick the "Prevent computer from sleeping automatically when the display is off" checkbox under System Preferences and 2) issue the following commands:

``````
sudo pmset autopoweroff 0
sudo pmset powernap 0
sudo pmset standby 0
sudo pmset proximitywake 0
sudo pmset tcpkeepalive 0
``````

This is a cautionary tale from someone who spent way more time than anyone should, **sleep it's most likely never going to happen**. Feel free to prove me wrong! :)

## About the RTC Fix

My current RTC exclusion list is defined permanently in the key `rtc-blacklist` as described here https://dortania.github.io/OpenCore-Post-Install/misc/rtc.html. 

The original value (unexpanded, stored in `boot-args`) was `rtcfx_exclude=58-89,b0-b7,d0-df`.

Important note: it seems that every BIOS update needs retesting because it might require the exclusion of additional addresses.
