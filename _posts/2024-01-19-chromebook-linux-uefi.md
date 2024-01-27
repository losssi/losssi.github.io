---
layout: single
tags:
title: Flash Chromebook UEFI for Linux Install
excerpt: Flashing UEFI on models Acer Chromebook 15, HP Chromebook 11 G3/G4 and HP Chromebook 14 G4
related: true
comments: true
header:
  teaser: /assets/images/posts/chromebook/chromebooks.jpg
---
![](/assets/images/posts/chromebook/chromebooks.jpg)

Chromebooks:
- Acer Chromebook 15 (CB3-531)
- HP Chromebook 11 G3/G4
- HP Chromebook 14 G4

## 1 Enable developer mode
To see the model of the board and determine how to disable write-protect.

1. Press **Esc** and **Refresh** down at the same time. Hold them and press **Power** button. Then release all three.

    Computer reboots and a white screen will appear with beeps
    ![](/assets/images/posts/chromebook/recovery.jpg)

2. Press **Ctrl** and **d**. Release both.

    Computer shows this screen and beeps

    ![](/assets/images/posts/chromebook/devmode.jpg)

3. Press **Enter**.

    *Don't re-enable, just WAIT every time you see this screen throughout reboots.*

    ![](/assets/images/posts/chromebook/re-enable.jpg)

According to *[https://mrchromebox.tech/#devices](https://mrchromebox.tech/#devices)* these Chromebooks were all write-protected with a screw.

## 2 Remove write-protect screw
This was easy to locate for Acers Banon board thanks to the info on mrchromebox.tech. The screws on KIP boards on HPs were harder to find.

1. Remove 18 (for Acer, less for HPs) screws to open case
2. Locate the wp screw and remove it

    Acer Chromebook 15
    ![](/assets/images/posts/chromebook/banon.jpg)

    HP Chromebook 11 G3/G4
    ![](/assets/images/posts/chromebook/kip3g.jpg)

    HP Chromebook 14 G4
    ![](/assets/images/posts/chromebook/kip1.jpg)

3. Screw the 18 (for Acer, less for HPs) screws back

## 3 Flash the ROM to your Chromebook

Log back in Developer mode as guest

  1. Log to your network
  2. Log in as guest
  3. Open crosh shell by pressing keys **Crtl**, **Alt** and **t** at the same time
  4. Copy the command from [https://mrchromebox.tech/#fwscript](https://mrchromebox.tech/#fwscript)

      ```shell
      cd; curl -LOk mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh
      ```

      ![](/assets/images/posts/chromebook/fwscript.jpg)

  5. Choose **Install/Update UEFI**

      The BIOS is still software locked so you need to remove write protect (**Y** and **Enter**) and reboot

      ![](/assets/images/posts/chromebook/reboot.jpg)  

  6. Repeat steps 2 to 5

## 4 Chromebook is ready for Linux install
![](/assets/images/posts/chromebook/bios.jpg)  
