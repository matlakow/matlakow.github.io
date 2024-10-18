---
layout: post
title:  "Hyper-V in Windows 10 and Windows 11 Home Edition"
author: matlakow
categories: [ Windows, hyper-V ]
image: assets/images/Hyper-V-b.png.webp
---


Hyper-V in Windows 10 and Windows 11 allows running Virtual Machine. It is supported only in Pro, Enterprise and Education Edition of Windows 10 and Windows 11 by default. But this guide will show you how to enable it in Home Editions of Windows 10 and Windows 11.

## Check if virtualization is enabled:

* Search for `Command Prompt` in Windows Start Menu and open it.
* Type `systeminfo` and press Enter. Wait for the process to finish
* Once the results appear, search for the `Hyper-V Requirements` section which is usually the last one.
  ![11](https://user-images.githubusercontent.com/61367380/141923469-48c99804-d491-497f-bcde-69de89f90045.jpg)
  * If it says `A hypervisor has been detected. Features required for Hyper-V will not be displayed.` that means Hyper-V is already enabled and there is no reason following this guide anymore.
  * Otherwise, check for `Virtualization Enabled in Firmware:`.
    * If its `Yes`, [click here](#Enable-Hyper-V).
    * If its `No`, [click here](#Enable-virtualization).

## Enable virtualization

The process of enabling virtualization can vary a lot depending on the motherboard manufacturer, but it can be summarized in a few steps:

* Completely Shutdown you Computer.
* Turn on the computer and while it is booting up, keep pressing the key to open the BIOS (usually it is `Del`, `F1`, `F2`, `F4`, `F11`, or `F12`). This key depends on the Motherboard manufacturer. You can easily google it out.
* Once you get into the BIOS, it may look very scary or intimidating, but don't worry, you will get it right. Mouse may not work in BIOS so you might have to use the Directional or Arrow keys and the Enter key of the Keyboard to navigate.
  * Search for the CPU configuration section, it can be called `CPU configuration`, `processor`, `Northbridge` or `Chipset` and may be under an `advanced` or `advanced mode` tab or menu.
  * Now you need to look for the virtualization option and enable it, it can have different names such as `Hyper-V`, `Vanderpool`, `SVM`, `AMD-V`, `Intel Virtualization Technology` or `VT-X`.
* Once its enabled, save and reboot your pc.
  If this part did not help you, you can specifically go the Website of the Mother Board Manufacturer of your Computer and ask for help there.

## Enable Hyper-V

* Open File Explorer on windows and Navigate to a folder.
* Right click anywhere in a blank space inside the folder. Select `New` and then Click `Text Document`.
* Open the file in Notepad and copy and paste the following text into it.
  ```
  pushd "%~dp0"
  dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
  for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
  del hyper-v.txt
  Dism /online /enable-feature /featurename:Microsoft-Hyper-V -All /LimitAccess /ALL
  pause
  ```
* Click `File` from the Menu bar in the top of Notepad, then click `Save as...`. In the Save as Window that appears, Change the File name to `"Hyper-V.bat"` and click save.
* Now Double click `Hyper-V.bat` to run it. This will take some time but will install all features required for Hyper-V. A Restart might be required after it is done.
* After Restarting Windows, search for `Turn Windows features on or off` in the Start Menu search bar and open it.
  ![10](https://user-images.githubusercontent.com/61367380/141923398-ee251035-8e1d-42e6-9551-5c797e2b8f73.png)
* In the Window, lookout for `Hyper-V`, `Virtual Machine Platform` and `Windows Hypervisor Platform`. Then check the check boxes before them and click `OK`. This will also take some time and then a Restart is necessary.

Thats it Hyper-V is enabled.
