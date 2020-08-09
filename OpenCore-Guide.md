# Open Core Guide for Intel Pc

# Firmware Drivers
Firmware drivers are drivers used by OpenCore in the UEFI environment. They're mainly required to boot a machine, either by extending OpenCore's patching ability or showing you different types of drives in the OpenCore picker(ie. HFS drives).

-   **Location Note**: These files **must** be placed under `EFI/OC/Drivers/`

# Universal
For the majority of systems, you'll only need 2 `.efi` drivers to get up and running:

-   [HfsPlus.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi)

[](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlus.efi)-   -   Needed for seeing HFS volumes(ie. macOS Installers and Recovery partitions/images). **Do not mix other HFS drivers**
-   [OpenRuntime.efi](https://github.com/acidanthera/OpenCorePkg/releases)
[](https://github.com/acidanthera/OpenCorePkg/releases)-   Replacement for [AptioMemoryFix.efi](https://github.com/acidanthera/AptioFixPkg)
[](https://github.com/acidanthera/AptioFixPkg)  used as an extension for OpenCore to help with patching boot.efi for NVRAM fixes and better memory management.


## Kexts

A kext is a **k**ernel **ext**ension, you can think of this as a driver for macOS, these files will go into the Kexts folder in your EFI.

-   **Windows and Linux note**: Kexts will look like normal folders in your OS, **double check** that the folder you are installing has a .kext extension visible(and do not add one manually if it's missing).
    -   If any kext also includes a `.dSYM` file, you can simply delete it. They're only for debugging purposes.
-   **Location Note**: These files **must** be placed under `EFI/OC/Kexts/`.

All kext listed below can be found **pre-compiled** in the [Kext Repo](http://kexts.goldfish64.com/)

[](http://kexts.goldfish64.com/). Kexts here are compiled each time there's a new commit

### Must haves required for all efi

Without the below 2, no system is bootable:

-   [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases)

[](https://github.com/acidanthera/VirtualSMC/releases)-  Emulates the SMC chip found on real macs, without this macOS will not boot
    -   Alternative is FakeSMC which can have better or worse support, most commonly used on legacy hardware.
-   [Lilu](https://github.com/acidanthera/Lilu/releases)
[](https://github.com/acidanthera/Lilu/releases) -   A kext to patch many processes, required for AppleALC, WhateverGreen, VirtualSMC and many other kexts. Without Lilu, they will not work

### VirtualSMC Plugins

The below plugins are not required to boot, and merely add extra functionality to the system like hardware monitoring:

-   SMCProcessor.kext
    -   Used for monitoring CPU temperature, **doesn't work on AMD CPU based systems**
-   SMCSuperIO.kext
    -   Used for monitoring fan speed, **doesn't work on AMD CPU based systems**
-   SMCLightSensor.kext
    -   Used for the ambient light sensor on laptops, **desktops can ignore**
    -   Do not use if you don't have an ambient light sensor, can cause issues otherwise
-   SMCBatteryManager.kext
    -   Used for measuring battery readouts on laptops, **desktops can ignore**
    -   Do not use until battery has been properly patched, can cause issues otherwise

### Graphics

-   [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases)
[](https://github.com/acidanthera/WhateverGreen/releases) -Used for graphics patching DRM, boardID, framebuffer fixes, etc, all GPUs benefit from this kext. 
-Note the SSDT-PNLF.dsl file included is only required for laptops.

###  Audio

-   [AppleALC](https://github.com/acidanthera/AppleALC/releases)
[](https://github.com/acidanthera/AppleALC/releases)
	-   Used for AppleHDA patching, used for giving you onboard audio. AMD 15h/16h may have issues with this and Ryzen/Threadripper systems rarely have mic support


### Ethernet 
Here we're going to assume you know what ethernet card your system has, reminder that product spec pages will most likely list the type of network card.

-   [IntelMausi](https://github.com/acidanthera/IntelMausi/releases)
[](https://github.com/acidanthera/IntelMausi/releases)
-  Required for the majority of Intel NICs, chipsets that are based off of I211 will need the SmallTreeIntel82576 kext
-  Intel's 82578, 82579, i217, i218 and i219 NICs are officially supported

- [AtherosE2200Ethernet](https://github.com/Mieze/AtherosE2200Ethernet/releases)[](https://github.com/Mieze/AtherosE2200Ethernet/releases)-    Required for Atheros and Killer NICs

- [RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X/releases) [](https://github.com/Mieze/RTL8111_driver_for_OS_X/releases)  - For Realtek's Gigabit Ethernet

-   [LucyRTL8125Ethernet](https://github.com/Mieze/LucyRTL8125Ethernet)[](https://github.com/Mieze/LucyRTL8125Ethernet)  -   For Realtek's 2.5Gb Ethernet

-   For Intel's i225-V NICs, patches are mentioned in the desktop Comet Lake DeviceProperty section. No kext is required.

###  USB
[USBInjectAll](https://github.com/Sniki/OS-X-USB-Inject-All/releases)[](https://github.com/Sniki/OS-X-USB-Inject-All/releases)-   -   Used for injecting Intel USB controllers on systems without defined USB ports in ACPI
    -   Not needed on Skylake (6th gen) and newer(AsRock is dumb and does need this)
    -   Does not work on AMD CPUs **at all**
    
-   [XHCI-unsupported](https://github.com/RehabMan/OS-X-USB-Inject-All)
[](https://github.com/RehabMan/OS-X-USB-Inject-All)
-   -   Needed for non-native USB controllers
    -   AMD CPU based systems don't need this
    -   Common chipsets needing this:
        -   H370
        -   B360
        -   H310
        -   Z390(Not needed on Mojave and newer)
        -   X79
        -   X99
        -   AsRock boards(On Intel motherboards specifically, Z490 boards do not need it however)


### WiFi and Bluetooth
[AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup/releases)[](https://github.com/acidanthera/AirportBrcmFixup/releases)-   -   Used for patching non-Apple Broadcom cards, **will not work on Intel, Killer, Realtek, etc**

-   [BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM/releases)
[](https://github.com/acidanthera/BrcmPatchRAM/releases)
-   -   Used for uploading firmware on Broadcom Bluetooth chipset, required for all non-Apple/Fenvi Airport cards.
    -   To be paired with BrcmFirmwareData.kext
        -   BrcmPatchRAM3 for 10.14+ (must be paired with BrcmBluetoothInjector)
        -   BrcmPatchRAM2 for 10.11-10.14
        -   BrcmPatchRAM for 10.10 or older

The order in `Kernel -> Add` should be:

1.  BrcmBluetoothInjector
2.  BrcmFirmwareData
3.  BrcmPatchRAM3



###  Extras
[AppleMCEReporterDisabler](https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip)[](https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip)-   -   Useful starting with Catalina to disable the AppleMCEReporter kext which will cause kernel panics on AMD CPUs and dual-socket systems
    -   Affected SMBIOS:
        -   MacPro6,1
        -   MacPro7,1
        -   iMacPro1,1
-   [CpuTscSync](https://github.com/lvs1974/CpuTscSync)
[](https://github.com/lvs1974/CpuTscSync)-   -   Needed for syncing TSC on some of Intel's HEDT and server motherboards, without this macOS may be extremely slow or even unbootable. Skylake-X should use TSCAdjustReset instead
-   [TSCAdjustReset](https://github.com/interferenc/TSCAdjustReset)
[](https://github.com/interferenc/TSCAdjustReset)-   On Skylake-X, many firmwares including Asus and EVGA won't write the TSC to all cores. So we'll need to reset the TSC on cold boot and wake. Compiled version can be found here: [TSCAdjustReset.kext](https://github.com/dortania/OpenCore-Install-Guide/blob/master/extra-files/TSCAdjustReset.kext.zip)
[](https://github.com/dortania/OpenCore-Install-Guide/blob/master/extra-files/TSCAdjustReset.kext.zip)-   -   . Note that you **must** open up the kext(ShowPackageContents in finder, `Contents -> Info.plist`) and change the Info.plist -> `IOKitPersonalities -> IOPropertyMatch -> IOCPUNumber` to the number of CPU threads you have starting from `0`(i9 7980xe 18 core would be `35` as it has 36 threads total)

-   [NVMeFix](https://github.com/acidanthera/NVMeFix/releases)[](https://github.com/acidanthera/NVMeFix/releases)
-- Used for fixing power management and initialization on non-Apple NVMe, requires macOS 10.14 or newer



### Laptop  Specifics

To figure out what kind of keyboard and trackpad you have, check Device Manager in Windows or `dmesg |grep input` in Linux

####  Input drivers

-   [VoodooPS2](https://github.com/acidanthera/VoodooPS2/releases)

[](https://github.com/acidanthera/VoodooPS2/releases)-   Required for systems with PS2 keyboards and trackpads
-   Trackpad users should also pair this with [VoodooInput](https://github.com/acidanthera/VoodooInput/releases)
[](https://github.com/acidanthera/VoodooInput/releases)-   -   (This must come before VoodooPS2 in your config.plist)
-   [VoodooI2C](https://github.com/alexandred/VoodooI2C/releases)
[](https://github.com/alexandred/VoodooI2C/releases)

-   -   Used for fixing I2C devices, found with some fancier touchpads and touchscreen machines
    -   To be paired with a plugin:
        -   VoodooI2CHID - Implements the Microsoft HID device specification.
        -   VoodooI2CElan - Implements support for Elan proprietary devices. (does not work on ELAN1200+, use the HID instead)
        -   VoodooI2CSynaptics - Implements support for Synaptic's proprietary devices.
        -   VoodooI2CFTE - Implements support for the FTE1001 touchpad.
        -   VoodooI2CUPDDEngine - Implements Touchbase driver support.

#### Misc

-   [NoTouchID](https://github.com/al3xtjames/NoTouchID/releases) [](https://github.com/al3xtjames/NoTouchID/releases)
--   -   Recommended for MacBook SMBIOS that include a TouchID sensor to fix auth issues, generally 2016 and newer SMBIOS will require this



##  SSDTs  -  ACPI
So you see all those SSDTs in the AcpiSamples folder and wonder whether you need any of them. For us, we will be going over what SSDTs you need in **your specific ACPI section of the config.plist**, as the SSDTs you need are platform specific. With some even system specific where they need to be configured and you can easily get lost if I give you a list of SSDTs to choose from now.

https://dortania.github.io/Getting-Started-With-ACPI/


**I2C Trackpad**   -- SSDT-XOSI.aml
**Backlight**
- [SSDT-PNLF.aml] -   For most users
- [SSDT-PNLF-CFL.aml] - For Coffee Lake and newer


**Embedded Controllers**

Desktop:
     
    SSDT-EC-USBX-DESKTOP
    For Skylake desktops and newer and AMD CPU based systems

    SSDT-EC-DESKTOP
    For Broadwell desktops and older

Laptops:

    SSDT-EC-USBX-LAPTOP.aml
    For Skylake laptops and newer

    SSDT-EC-LAPTOP.aml
    For Broadwell laptops and older

 Power Management (SSDT-PLUG)
