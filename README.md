# wsl2usb 
This document explains how to activate your usb devices in the WSL2 on windows 11.

## install usbipd

open windows powershell in administrator mode and type this.

```sh
winget install usbipd
```

and close the terminal and reopen the powershell in admin mode again

## setup

type usbpid list then you will get like this
```sh
PS C:\Windows\system32> usbipd list
Connected:
BUSID  VID:PID    DEVICE                                                        STATE
1-2    1050:0407  USB Input Device, Microsoft Usbccid Smartcard Reader (WUDF)   Not shared
1-6    0c45:6732  Integrated Webcam, Integrated IR Webcam                       Not shared
1-9    27c6:63ac  Goodix MOC Fingerprint                                        Not shared
1-10   8087:0033  Intel(R) Wireless Bluetooth(R)                                Not shared
4-4    0bda:8153  Realtek USB GbE Family Controller #2                          Not shared
5-5    413c:b06e  USB Input Device, Dell Performance Dock WD19DCS               Not shared
7-2    046d:08e5  HD Pro Webcam C920                                            Not shared
7-5    413c:b06f  USB Input Device                                              Not shared
8-1    046d:0a64  Logi USB Headset, USB Input Device                            Not shared
8-2    413c:2514  USB Input Device                                              Not shared
10-1   2341:0043  USB Serial Device (COM7)                                      Not shared
10-2   413c:b09e  Dell DA20 Adapter                                             Not shared
```

STATE shows which of your usb device is being shared with WSL system. As it stated none of these are not shared yet.

Pick one device you want to share. In this example I'd like to share BUSID 10-1 
```bash
# share the device
PS C:\Windows\system32> usbipd bind --busid 10-1
```

and check the list again

```sh
PS C:\Windows\system32> usbipd list
Connected:
BUSID  VID:PID    DEVICE                                                        STATE
1-2    1050:0407  USB Input Device, Microsoft Usbccid Smartcard Reader (WUDF)   Not shared
1-6    0c45:6732  Integrated Webcam, Integrated IR Webcam                       Not shared
1-9    27c6:63ac  Goodix MOC Fingerprint                                        Not shared
1-10   8087:0033  Intel(R) Wireless Bluetooth(R)                                Not shared
4-4    0bda:8153  Realtek USB GbE Family Controller #2                          Not shared
5-5    413c:b06e  USB Input Device, Dell Performance Dock WD19DCS               Not shared
7-2    046d:08e5  HD Pro Webcam C920                                            Not shared
7-5    413c:b06f  USB Input Device                                              Not shared
8-1    046d:0a64  Logi USB Headset, USB Input Device                            Not shared
8-2    413c:2514  USB Input Device                                              Not shared
10-1   2341:0043  USB Serial Device (COM7)                                      Shared
10-2   413c:b09e  Dell DA20 Adapter                                             Not shared

```

It doesn't mean that you can access the device in WSL. You need to attach it to WSL system by doing this:

```sh
# attach the device
PS C:\Windows\system32> usbipd attach --wsl --busid 10-1
usbipd: info: Using WSL distribution 'Ubuntu-24.04' to attach; the device will be available in all WSL 2 distributions.
usbipd: info: Loading vhci_hcd module.
usbipd: info: Detected networking mode 'mirrored'.
usbipd: info: Using IP address 127.0.0.1 to reach the host.
```
__NOTICE__ this step requires that WSL is active. So you need to open wsl terminal first by doing this step.

Now I can see the serial device in /dev/ttyACM0 in WSL

```sh
crw--w----  1 root tty       4,   1 Dec 17 09:50 tty1
crw-------  1 root root      5,   1 Dec 17 09:50 console
crw-rw-rw-  1 root tty       5,   0 Dec 17 11:03 tty
drwxr-xr-x  3 root root          60 Dec 17 11:17 bus
crw-rw----  1 root dialout 166,   0 Dec 17 11:17 ttyACM0
drwxr-xr-x  4 root root          80 Dec 17 11:17 serial
```

You may get a warning message like this "usbipd: warning: Unknown USB filter 'CsDeviceControl' may be incompatible with this software; 'bind --force' may be required." but you can ignore it.

You can also detach the device by using this command or unplug the device.

```sh
# detach the device
PS C:\Windows\system32> usbipd detach --wsl --busid 10-1
```
