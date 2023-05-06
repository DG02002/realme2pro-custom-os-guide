# Realme 2 Pro (RMX1801)

This is a guide on installing CustomOS on the [Realme 2 Pro](https://www.gsmarena.com/realme_2_pro-9345.php). We strongly recommend following these instructions. This guide has a lot of collaborative effort put into covering all of the edge cases and is regularly tested by many people on each supported OS.

## Prerequisites

You should have at least 2GB of free memory available and 8GB of free storage space.
Standards compliance requires an Intel processor and USB-A 2.0, AMD Ryzen and USB-A 3.0 are problematic.
You need a USB cable for attaching the device to a laptop or desktop. Whenever possible, use the high quality standards compliant USB-Micro 2.0 to USB-A cable packaged with the device. You should avoid using a USB hub such as the front panel on a desktop computer case. Connect directly to a rear port on a desktop or the ports on a laptop. Many widely distributed USB cables and hubs are broken and are the most common source of issues for installing OS.
Installing from an OS in a virtual machine is not recommended. USB passthrough is often not reliable. To rule out these problems, install from an OS running on bare metal. Virtual machines are also often configured to have overly limited memory and storage space.
Officially supported operating systems for the CLI install method:

- Windows 10
- Windows 11

Make sure your operating system is up-to-date before proceeding.
It's best practice to update the device before installing CustomOS to have the latest firmware for connecting the phone to the computer and performing the early flashing process.

## Table of contents

 * [Enabling Developer options](#enabling-developer-options)
   + [OEM unlocking](#enabling-oem-unlocking)
   + [Camera2 API](#enabling-camera2-api)
 * [Opening terminal](#opening-terminal)
 * [Standalone platform-tools](#standalone-platform-tools)
 * [Checking fastboot version](#checking-fastboot-version)
 * [Booting into the bootloader interface](#booting-into-the-bootloader-interface)
 * [Connecting the phone](#connecting-the-phone)
 * [Unlocking the bootloader](#unlocking-the-bootloader)
 * [Installing a custom recovery using fastboot](#installing-a-custom-recovery-using-fastboot)
 * [Installing CustomOS from recovery](#installing-customos-from-recovery)
 * [SafetyNet and Play Integrity API](#safetynet-and-play-integrity-api)
 * [Gapps & Vanilla Builds](#gapps--vanilla-builds)
   + [GApps Build](#gapps-build)
   + [Vanilla Build](#vanilla-build)
 * [Google apps](#google-apps)
   + [MindTheGApps](#mindthegapps)
   + [NikGApps](#nikgapps)
 * [CustomOS Lists](#customos-lists)
 * [Post-installation Notes for CustomOS](#post-installation-notes-for-customos)
 * [Links](#links)

## Enabling Developer options

Enable the developer options menu by going to Settings ➔ About phone ➔ Verison and repeatedly pressing the build number menu entry until developer mode is enabled.

## Enabling OEM unlocking

OEM unlocking needs to be enabled from within the realme UI.
Next, go to Settings ➔ Additional Settings ➔ Developer options and toggle on the `OEM unlocking` setting.

## Enabling Camera2 API

Camera2 API unlocking needs to be enabled from within the realme UI.
Next, go to Settings ➔ System ➔ Developer options in Apps section toggle on the `Camera HAL3` setting.

## Opening terminal

These instructions use command-line tools. Launch the terminal as you would any other application. On Windows, launch a regular non-administrator instance of the PowerShell terminal. Do not use the legacy Command Prompt or administrator variant of PowerShell.
Use the same terminal for the whole installation process. If you close it, you'll lose the setup of the environment for the installation.
On Windows, run the following command to remove PowerShell's legacy curl alias for the current shell to avoid needing to reference it as `curl.exe` instead of `curl`:

```powershell
Remove-Item Alias:Curl
```

## Standalone platform-tools

If your operating system doesn't include a usable version of fastboot, you can use the official standalone releases of platform-tools. This is our recommendation for most users. The flashing process won't work unless you follow these instructions including setting up PATH.
To download, verify and extract the standalone platform-tools on Windows:

```powershell
curl -O https://dl.google.com/android/repository/platform-tools_r33.0.3-windows.zip
tar xvf platform-tools_r33.0.3-windows.zip
```

Next, add the tools to your `PATH` in the current shell so they can be used without referencing them by file path, enabling usage by the flashing script.
On Windows:

```powershell
$env:Path = "$pwd\platform-tools;$env:Path"
```

This only changes `PATH` for the current shell and will need to be done again if you open a new terminal.

## Checking fastboot version

Check the output of `fastboot --version` before continuing.
Example of the output after following the instructions above for the standalone platform-tools:

```powershell
fastboot version 33.0.3-8952118
Installed as /home/username/platform-tools/fastboot
```

## Booting into the bootloader interface

Alternatively, you need to boot your phone into the bootloader interface. To do this, you need to hold the volume down button while the phone boots.
The easiest approach is to reboot the phone and begin holding the volume down button until it boots up into the bootloader interface.
Alternatively, turn off the phone, then boot it up while holding the volume down button during the boot process. You can either boot it with the power button or by plugging it in as required in the next section.

## Connecting the phone

On Windows, you need to install a driver for fastboot if you don't already have it. No driver is needed on other operating systems. You can obtain the driver from Windows Update which will detect it as an optional update when the device is booted into the bootloader interface and connected to the computer. Open Windows Update, run a check for updates and then open the "View optional updates" interface. Install the driver for the Android bootloader interface as an optional update.

## Unlocking the bootloader

Once the device is in fastboot mode, verify your PC finds it by typing:

```powershell
fastboot devices
```

Unlock the bootloader to allow flashing the OS and firmware:

```powershell
fastboot flashing unlock
```

The command needs to be confirmed on the device and will wipe all data. Use one of the volume buttons to switch the selection to accepting it and the power button to confirm.
!!! note Note
    After Unlocking the bootloader make sure that `Camera2 API` is still enabled.

## Installing a custom recovery using fastboot

Download a custom recovery from link: [TWRP](https://drive.google.com/drive/folders/1a8GPif-abIBVcknYVDszbRcKsbO-9RLT?usp=share_link), [OrangeFox](https://drive.google.com/drive/folders/18WXmdBDTCRQDoPPnqzoWNnHSRfL9W7RK?usp=share_link)
Flash a recovery on your device by typing (replace ``<recovery_filename>`` with the actual filename):

```powershell
fastboot flash recovery <recovery_filename>.img
```

## Installing CustomOS from recovery

If you are not in recovery, reboot into recovery:

```powershell
fastboot reboot recovery
```

Other way hold down `Volume Up` + `Power key` together for a short while.
Format Data and continue with the formatting process. This will remove encryption and delete all files stored in the internal storage.
Wipe Partitions

```powershell
Dalvik / ART Cache, System, Cache, Data, Internal storage, Vendor
```

Install CustomOS from `SD card / USB flash drive`.

## SafetyNet and Play Integrity API

SafetyNet is a system put in place by Google to detect modified devices, and devices which aren’t Google Play Certified. This means if you modify your device, SafetyNet is likely going to detect that.
Many applications, for example Banking Apps, or Nintendo’s Mobile Games check for Root Access using SafetyNet or their own methods.
Ever since the Magisk 24 release, Magisk Hide - a “works-but-isnt-great” solution to hiding Root Access - got discontinued and removed from the Application In the same version, Zygisk got introduced, allowing Magisk Modules to modify apps and services at runtime
From there, Kdrag0n and other developers started developing (or improving) projects like Universal SafetyNet Fixer, Shamiko, and many others, to hide Root Access even better than before
There are many tutorials out there on how to install these projects, though, I will instead leave links;

- <https://github.com/topjohnwu/Magisk>
- <https://github.com/kdrag0n/safetynet-fix>
- <https://github.com/Magisk-Modules-Repo/MagiskHidePropsConf>

## Gapps & Vanilla Builds

### GApps Build

GApps Build came with pre installed Google Apps and services such as the Play Store, Gmail, Maps, etc.

### Vanilla Build

Vanilla Build are mostly for the people that don’t want Google services to be on their tails. And Vanilla can be customized by the user however they would like, it can be used as FOSS, It can be used with GApps. Most users that want FOSS software are using Vanilla ROMs such as LineageOS, GrapheneOS, and AOSP in general.

## Google apps
>
> Google apps should be installed via recovery immediately after installing CustomOS.
Like the Android Open Source Project, Vanilla Build doesn't include Google apps and services.
If you use a larger package, we can not guarantee that everything will function on your device, as in many of these cases our included apps are overwritten in favor of the Google App equivalents. If your device states that there is not enough space on any specific partition during install, you will need to use an even smaller package instead.

### MindTheGApps

Speaking of GApps and Vanilla, LineageOS is the resurrection of what CyanogenMod was at the time. MindTheGApps only and only has the user package, which has most of the Google Apps inside, ready to be flashed and uses as is. MindTheGApps is highly recommended by LineageOS developers.
Filenames on MindTheGapps are of the format `MindTheGapps-<AndroidVersion>-<architecture>-<date>_<time>.zip` (with Android 12L being 12.1), choose carefully!
<https://androidfilehost.com/?w=files&flid=322935>

### NikGApps

NikGApps is another feature-rich GApps distribution that offers extensive customization options for power users. Created by XDA Senior Member [Nikhil](https://forum.xda-developers.com/m/nikhil.4867515/), one of the most distinguishing features of this GApps project is it comes with a dedicated Android Go package for low end devices. Unlike other distributions, it also allows you to dirty flash the package anytime you want.
Since you can install any non included apps later, we only recommend the following package sizes (or smaller):
`CORE`, as described in Downloads page
<https://nikgapps.com/>

## CustomOS Lists

This list, which is sorted by when it was most recently updated, includes only popular OS and official releases.
| Name      | Version | SafetyNet and Play Integrity API | Official Support | Last Updated (YYYY/MM/DD) | Managed By | Download Link |
| ----------- | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: |
| [NusantaraProject](https://nusantararom.org/device/rmx1801/) | Android 13 | Pass | `Active` | 2023/04/18 | Irawan | [Plings](https://www.pling.com/p/1422375/) |
| [ArrowOS](https://www.arrowos.net/) _OPPO ➔ RMX1801_ | Android 13 | Pass | `Dropped` | 2023/01/10 | Baibhab | [Github](https://github.com/semisoft0072/RMX1801/releases/tag/ArrowOS) |
| [StagOS](https://stag-os.org/) | Android 12.1 | Pass | `Dropped` | 2022/08/31 | Baibhab | [SOURCEFORGE](https://sourceforge.net/projects/stagos-12/files/RMX1801/) |
| [LineageOS 17.1](https://wiki.lineageos.org/devices/RMX1801/) | Android 10 | Fail | `Dropped` | 2022/02/09 | sb6596 | [Github](https://github.com/semisoft0072/RMX1801/releases/tag/LineageOS) |
| [AwakenOS](https://github.com/Project-Awaken) | Android 12 | Pass | `Dropped` | 2022/01/26 | Ad1tyaS1ngh | [SOURCEFORGE](https://sourceforge.net/projects/project-awaken/files/r2p/) |
| [Project Elixir](https://projectelixiros.com/device/RMX1801) | Android 12 | Pass | `Dropped` | 2022/01/24 | Abhishek | [Plings](https://www.pling.com/p/1628787/) |
| [PixelPlusUI](https://ppui.site/device/Realme2Pro) | Android 12 | Pass | `Dropped` | 2022/01/22 | Abhishek | [Plings](https://www.pling.com/p/1523104/) |
| [PixelExperience](https://download.pixelexperience.org/r2p/) | Android 12 | Pass | `Dropped` | 2021/12/21 | Ad1tyaS1ngh | [Pixelexperience](https://download.pixelexperience.org/r2p/) |

## Post-installation Notes for CustomOS

- Never Disabling OEM unlocking
- Never Lock the flashing unlock/bootloader

## Links

- [SDK Platform Tools](https://developer.android.com/studio/releases/platform-tools)
- [USB Driver](https://androidfilehost.com/?fid=6006931924117944052)
- [Firmware](https://github.com/sb6596/firmware_files_RMX1801/releases)
- [realme UI Decrypted/Encrypted](https://docs.google.com/spreadsheets/d/13HbRttDfsdwge9GNc6cuAzDwGM7CwYb9mb84b2O9zK4)
- [Files on Telegram](https://t.me/realme2pro)
- [Realme 2 Pro | News & Updates](https://t.me/rm2pro_updates)
- [Realme 2 Pro | R2X2](https://t.me/realme2proXDA)
- [Realme 2 Pero forever](https://t.me/sujitwork)
- <https://github.com/RMX1801>
