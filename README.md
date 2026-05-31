# Enable SSH / Root on Xiaomi Redmi AX5400 Gaming Edition Router (MiWiFi 1.0.95)

**English** · [Русский](README.ru.md)

Step-by-step guide to unlock SSH and get root access on Xiaomi / Redmi routers **without flashing custom firmware**. Tested on the **Redmi AX5400 Gaming Edition** running stock **MiWiFi 1.0.95**, using the STOK web exploit and the MiWiFi default-password calculator.

## Supported routers

The same method works on several Xiaomi / Redmi models on a vulnerable stock MiWiFi version (downgrade first if yours is newer). Recommended firmware per model:

| Router | MiWiFi version |
| --- | --- |
| Redmi AX5400 Gaming/Esports Edition | 1.0.95 |
| Redmi AX3000 | 1.0.33 |
| Xiaomi AX3000 | 1.0.48 / 1.0.46 |
| Xiaomi AX1800 | 1.0.399 |
| Xiaomi AX9000 | 1.0.165 |
| Xiaomi AIoT AX3600 | 1.1.21 |
| Xiaomi AC2100 | 2.0.743 |
| Xiaomi AIoT AC2350 | 1.3.8 |
| Xiaomi 10 Gigabit Router | 1.0.53 |

Full details and the original research are in the [mirrored forum guide](BACKUP.md).

### Thanks to `Minorice`

### Original post: https://www.right.com.cn/forum/thread-8348455-1-1.html

### [Full forum guide — all models (mirror)](BACKUP.md)

### [HTML Forum backup](https://github.com/dary1337/unlock-ssh-xiaomi-router/releases/tag/forum_backup)

### [How to setup router (AX5400) in WISP mode](https://github.com/dary1337/wisp-mode-redmi-ax5400)

# My case:

### Router: 
- Xiaomi Router AX5400 Gaming Edition

### Stable Version MiWiFi:
- 1.0.95

# How to enable SSH on MiWiFi (step by step)

### Preparation:

1) Factory reset the router

2) Setup router in `Router` mode
   - I used DHCP

### Installation:

3) Open Web router page http://192.168.31.1/

4) Get the STOK value from url (f.e. ;stok=`57a3a045a1db66a1573a1b2e4ef3ccb8`)

5) Download [enable_ssh.cmd](https://raw.githubusercontent.com/dary1337/unlock-ssh-xiaomi-router/refs/heads/master/enable_ssh.cmd) script 

6) Update [enable_ssh.cmd](https://raw.githubusercontent.com/dary1337/unlock-ssh-xiaomi-router/refs/heads/master/enable_ssh.cmd) script with your STOK value 

 - > set TOKEN=57a3a045a1db66a1573a1b2e4ef3ccb8

7) Run [enable_ssh.cmd](https://raw.githubusercontent.com/dary1337/unlock-ssh-xiaomi-router/refs/heads/master/enable_ssh.cmd) script

- If there is an error, you can try to run it again

8) Open Web router page and reset the time of router

### Connection:

9) Copy Serial Number from Web router page (f.e. `37668/A1ZZ16727`)

10) Get the default password: [MiWiFi.DEV SSH Passwd Calculator](https://miwifi.dev/ssh)

11) Open command line and run: `ssh -oHostKeyAlgorithms=+ssh-rsa root@192.168.31.1`

12) Fill the password from `step 9` (by clicking RMB)

### Restore ssh after reboot:

You can relaunch script  
Result will be like that:
```
{"code":0}
{ "code": 0, "msg": "", "id": 42 }
{"code":-101,"msg":"request server timeout"}
{ "code": 0, "msg": "", "id": 43 }
{"code":-101,"msg":"request server timeout"}
{ "code": 0, "msg": "", "id": 44 }
{"code":-101,"msg":"request server timeout"}
{ "code": 0, "msg": "", "id": 45 }
{"code":-101,"msg":"request server timeout"}
{ "code": 0, "msg": "", "id": 46 }
{"code":-101,"msg":"request server timeout"}
end
```

### [How to setup router (AX5400) in WISP mode](https://github.com/dary1337/wisp-mode-redmi-ax5400)
