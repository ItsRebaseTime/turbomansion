# Control panel

![control panel](/units/control_panel/assets/2022-4-6-inch-Tuya-Smart-Home-Multi-functional-Touch-Screen-Control-Panel-Gateway-Light-Switch.jpg)

## Features

| Feature            | Comment                                |
| ------------------ | -------------------------------------- |
| 4" IPS touchscreen | Fully working and nicely responsive    |
| Ethernet           | 802.3-2008, 10/100Mbps                 |
| WiFi               | 802.11b/g/n, 150Mbps                   |
| Bluetooth          | Bluetooth v4.2 BR/EDR and Bluetooth LE |
| ZigBee             | Working                                |
| RS485              | Seems to be not available              |
| 2 Relays           | Working                                |
| Speaker            | Working                                |
| Microphones        | Working                                |
| Proximity sensor   | Working                                |

## Parts list

| Part             | Datasheet                                                                    | Shop                                                                |
| ---------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| S6E smart screen | [link](/units/control_panel/docs/S6E_4_Smart_Control_Panel_in-wall_SPEC.pdf) | [aliexpress](https://www.aliexpress.com/item/1005004767029653.html) |

## Setup

To start I would recommend taking a look these sources:

1. Super awesome guy Blakadder did most of the work for this, here are a few of his blogposts:
   https://blakadder.com/t6e-sideload/
   https://blakadder.com/android-panel-webview/

2. A video and blogpost by [Home Automation Guy](https://www.youtube.com/@HomeAutomationGuy):
   https://www.homeautomationguy.io/blog/home-assistant-wall-panels-with-a-tuya-s6e
   [![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/EuVUarY-Bh0/0.jpg)](https://www.youtube.com/watch?v=EuVUarY-Bh0 "Video")

### TLDR:

1. Download and install the [Google USB driver](https://developer.android.com/studio/run/win-usb)
2. Download, extract, and add to your system path the [Android Platform Tools (ADB)](https://dl.google.com/android/repository/platform-tools-latest-windows.zip)
3. Download [Ultra Small App Launcher](https://blakadder.com/assets/files/ultra-small-launcher.apk)
4. Download [Xposed Installer](https://www.apkmirror.com/apk/rovo89/xposed-installer/xposed-installer-3-1-5-release/xposed-installer-3-1-5-android-apk-download/)
5. Carefully disassemble your specific panel (no need for S8E)
6. Connect the panel to your computer via a USB cable
7. Check if the panel is detected:
   `adb devices -l`
   If panel is detected you will see something similar to this:
   `D:\adb>adb devices -l`
   `List of devices attached`
   `F061512302021100016 device product:px30_evb model:px30_evb device:px30_evb transport_id:3`
8. Set ADB to connect over TCP/IP:
   `adb tcpip 5555`
9. Get panel IP address:
   `adb shell ip -o a`
10. Connect to the panel:
    `adb connect [panel_ip]`
11. Set the panel to always listen for ADB over IP:
    `adb shell setprop persist.adb.tcp.port 5555`
12. Disconnect the panel from USB and assemble it
13. Power up and connect the panel to wired or wireless network
14. Connect to the panel via the new IP:
    `adb connect [new_panel_ip]`
15. Install ultra small launcher:
    `adb install ultra-small-launcher.apk`
16. Simulate home hey press:
    `adb shell input keyevent 3`
17. Select the new launcher
18. Disable Tuya spyware:
    `adb shell`
    `su`
    `pm disable com.tuya.iotgateway.launcher`
    `pm disable com.smartpad.fourinchneeu.smart` or for global models `pm disable com.smartpad.fourinchnegl.smart`
19. Install Xposed installer:
    `adb install xposed_installer.apk`

## Home Assistant

### Cards
