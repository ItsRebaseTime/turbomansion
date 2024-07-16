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
5. Download [Xposed framework](https://blakadder.com/assets/files/xposed-v90-sdk27-arm64-beta3.tar)
6. Download [AnyWebView](https://github.com/neoblackxt/AnyWebView/releases/)
7. Download [Fully Kiosk Browser](https://www.fully-kiosk.com/files/2024/05/Fully-Kiosk-Browser-v1.55.3.apk)
8. Download [J Touch](https://apkpure.com/j-touch/com.bs.smarttouch.gp/downloading)
9. Carefully disassemble your specific panel (no need for S8E)
10. Connect the panel to your computer via a USB cable
11. Check if the panel is detected

    `adb devices -l`

    If panel is detected you will see something similar to this

    `D:\adb>adb devices -l`
    `List of devices attached`
    `F061512302021100016 device product:px30_evb model:px30_evb device:px30_evb transport_id:3`

12. Set ADB to connect over TCP/IP

    `adb tcpip 5555`

13. Get panel IP address

    `adb shell ip -o a`

14. Connect to the panel

    `adb connect [panel_ip]`

15. Set the panel to always listen for ADB over IP:

    `adb shell setprop persist.adb.tcp.port 5555`

16. Disconnect the panel from USB and assemble it
17. Power up and connect the panel to wired or wireless network
18. Connect to the panel via the new IP

    `adb connect [new_panel_ip]`

19. Install ultra small launcher

    `adb install ultra-small-launcher.apk`

20. Simulate home key press

    `adb shell input keyevent 3`

21. Select the new launcher
22. Install Fully Kiosk Browser

    `adb install Fully-Kiosk-Browser.apk`

23. Install J Touch

    `adb install "jtouch.apk"`

24. Launch J Touch and set it up so you could navigate without adb keyevent commands
25. Install Xposed installer

    `adb install xposed_installer.apk`

26. Move Xposed framework archive to panel's internal storage

    `adb push xposed.tar /sdcard/Download/`

27. Enter shell

    `adb shell`

28. Invoke root permissions

    `su`

29. Disable Tuya spyware

    `pm disable com.tuya.iotgateway.launcher`

30. Remove annoying overlay

    `pm disable com.smartpad.fourinchneeu.smart`

    Or for global models

    `pm disable com.smartpad.fourinchnegl.smart`

31. Make filesystem writeable

    `mount -o rw,remount /system`

32. Navigate to the directory where Xposed framework is located

    `cd /sdcard/Download/`

33. Extract the xposed.tar file

    `tar -xvf xposed.tar`

34. Change into the new directory

    `cd /sdcard/Download/xposed/`

35. Make the installer script executable

    `chmod a+x /sdcard/Download/xposed/META-INF/com/google/android/flash-script.sh`

36. Run Xposed framework installer script

    `sh /sdcard/Download/xposed/META-INF/com/google/android/flash-script.sh`

37. Reboot, it may take some time

    `reboot`

38. Open launcher and launch the Xposed app

    `adb shell input keyevent 3`

39. Check if the Xposed status is active
40. Install AnyWebView

    `adb install AnyWebView.apk`

41. Open Xposed again, open Modules from the left menu and activate AnyWebView.
42. Reboot, this time it should be faster

    `adb shell reboot`

43. Install WebView, it may take a while

    `adb install "WebView.apk"`

44. Go to Settings -> System -> About tablet -> Build Number and tap the “Build Number” option 7 times to enable developer options.
45. Go to Developer options, navigate to WebView Implementation and choose your WebView instead of the outdated “Android System WebView 61.x”
46. Open Fully Kiosk Browser and set it up for your Home Assistant installation

## Home Assistant

### Cards
