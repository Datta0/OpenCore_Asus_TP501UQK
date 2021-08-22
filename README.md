# Asus Vivobook Flip TP501UQK Opencore Hackintosh 

![Screen Shot](https://user-images.githubusercontent.com/39181234/130348561-5d96e473-640b-4456-8d2d-ac06dee89667.png)

| Specs | Details |
|------------|-------------------------------|
| Model| Asus Vivobook Flip TP501UQK |
| OpenCore | 0.7.2 |
| OS | macOS Big Sur 11.5.2 |
| CPU | Intel(R) Core(TM) i7 7500U |
| RAM | 12 GB DDR4 2133MHz |
| iGPU | Intel HD Graphics 620 |
| dGPU | NVIDIA GeForce 940MX |
| Touchpad | ELAN |
| Wireless | Intel AC 8260 NGW |
| Audio | ALC255 |


__Tested and working__

- [x] Intel HD Graphics 620
- [x] USB 3.0, USB C
- [x] Webcam
- [x] LAN
- [x] Screen brightness (Keybroad funtion support)
- [x] Battery status
- [x] Sleep/Wake
- [x] TouchPad with gestures
- [x] WiFi + Bluetooth
- [x] HDMI
- [x] Audio + Headphone + Internal Mic 
- [x] Keyboard 
- [x] iServices (Asked for sign in, then works fine ig)

__Not working__

- [ ] NVIDIA GeForce GTX 1050M
- [ ] DRM
- [ ] SD Card reader




## Guide

__BIOS Settings (Version 1.08)__

- Disable Secure Boot
- Disable Vt-d


__OpenCore config__

- Follow these instructions to configure your OpenCore: https://dortania.github.io/OpenCore-Install-Guide/
- Modifying SSDTs : https://dortania.github.io/Getting-Started-With-ACPI/  
- ACPI Hotpatch: https://github.com/daliansky/OC-little  
- Audio layout ID 13


__USB Mapping__

- USB Mapping: https://dortania.github.io/OpenCore-Post-Install/usb/intel-mapping/intel.html
- USB Mapping: https://github.com/corpnewt/USBMap
- USB Mapping: https://github.com/USBToolBox/tool
- Or using Hackintool

__Fix HDMI audio__

- Patch framebuffer: https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md
- Add kext: `FakePCIID.kext` (Not Tested, works acc to [vacnex](https://github.com/vacnex/VX5-591G-OpenCore))

__Fix iServices__

- Fix iServices: https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html

__Fix brightness key__

Use patch `SSDT-BKEY.aml`  
And at `Root > ACPI > Patch`:  
- XQ11 Rename

|Key|Type|Value|
|---|---|---|
|Comment|String|Change _Q11 to XQ11 (Brightness Down)|
|Count|Number|0|
|Enabled|Boolean|True|
|Find|Data|5F513131|
|Limit|Number|0|
|Replace|Data|58513131|

- XQ12 Rename

|Key|Type|Value|
|---|---|---|
|Comment|String|Change _Q12 to XQ12 (Brightness Up)|
|Count|Number|0|
|Enabled|Boolean|True|
|Find|Data|5F513132|
|Limit|Number|0|
|Replace|Data|58513132|


__Fix Dual Boot__

I use macos and window 10 on different drives. When I installed Windows, it automatically placed the boot folder in Opencore Partition, so it is autodected.

If it doesn't work for you follow below as written by [vacnex](https://github.com/vacnex/VX5-591G-OpenCore)

To dual boot mac and windows you should change `\EFI\Microsoft\Boot\bootmgfw.efi` to `\EFI\Microsoft\Boot\bootmgfw-orig.efi`
Then OC should be detect your winows 10 efi in Boot menu

**IF OC Bootloader NOT AUTO DETECT WINDOWS 10 EFI** follow below steps:

#### Bless Override

- In your config.plist at `Misc/BlessOverridedd` add new child `\EFI\Microsoft\Boot\bootmgfw-orig.efi`

- Then reboot you should see your EFI partition appear, but you can not rename this entry.
  
#### Add custom Windows 10 entry

- First you should add `OpenShell.efi` in your `OC\driver` and add it to `config.plist` to find Pciroot

- Boot Into OpenCore and Select `OpenShell.efi`, you’ll be Greeted with Screen like this (image from another one).

![openshell](https://preview.redd.it/am2ilw28p7251.png?width=946&format=png&auto=webp&s=123cdc6fc67da00a2818bac4c226196d0d4f2f39)
  
- Select your EFI Partition (The first entry of your boot drive. In my case it FS0:, the first ssd entry)
  
- Enter `map > \map_table.txt` to save your drive map data. Remember which FS drive your EFI is!
  
- Now exit from OpenShell and boot Mac OS.
  
- Get into your EFI drive (I'm assuming you're old hat at this by now) and open map_table.txt Note the device string that identifies your EFI drive.

![map_table](https://i.imgur.com/uQb3YTx.png)
  
- Edit your `config.plist` file with the following changes:
  
- At `Misc -> Entries` create a new item with the following:
  
  |Key|Type|Value|
  |---|---|---|
  |Comment|String| Windows 10 Entry |
  |Name|String|Windows 10|
  |Enabled|Boolean|YES|
  |Auxiliary|Boolean|NO|
  |Arguments|String|    
  |Path|String| **Your EFI Device String**/\EFI\Microsoft\Boot\bootmgfw-orig.efi|
  
  As an example, this is vacnex's Path string:
  
  >`PciRoot(0x0)/Pci(0x17,0x0)/Sata(0x0,0x0,0x0)/HD(1,GPT,000001B3-2A80-3AAD-A6DE-D50166030000,0x800,0x64000) /\EFI\Microsoft\Boot\bootmgfw-orig.efi`
  
  > Remember space before `/\EFI\`
  
- At `Misc -> Boot -> LauncherOption`, set to **Full**

- At `Misc -> Security -> ScanPolicy` set to **0**



__Fix timezone:__ 
https://www.tonymacx86.com/threads/fix-incorrect-time-in-windows-osx-dual-boot.133719/  


## Credits
Thanks to Vacnex, Acidanthera, RehabMan, dortania, alexandred, daliansky, dongcodebmt
