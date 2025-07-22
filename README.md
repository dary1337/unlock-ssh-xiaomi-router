# Unlock ssh for Xiaomi Routers

### Thanks to `Minorice`

### Original post: https://www.right.com.cn/forum/thread-8348455-1-1.html

### [Markdown version](https://github.com/dary1337/unlock-ssh-xiaomi-router/blob/master/BACKUP.md)

### [HTML Forum backup](https://github.com/dary1337/unlock-ssh-xiaomi-router/releases/tag/forum_backup)

### [How to setup router (AX5400) in WISP mode](https://github.com/dary1337/wisp-mode-redmi-ax5400)

# My case:

### Router: 
- Xiaomi Router AX5400 Gaming Edition

### Stable Version MiWiFi:
- 1.0.95

# My Steps:

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
