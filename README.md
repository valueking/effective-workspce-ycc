一、巴法云的使用
巴法云网址https://cloud.bemfa.com/，可以用微信进行注册。手机APP见首页顶部巴法APP，应用商店也可以搜到。
![image](https://github.com/valueking/effective-workspce-ycc/assets/167502049/cf1ebf1b-6589-4d1c-8a03-b2f66f0d68c4)


注册登录后选择右上的控制台，进行入控制台界面。选择下图中的“MQTT设备云”。
![image](https://github.com/valueking/effective-workspce-ycc/assets/167502049/a0695b48-d0e4-413d-a925-a88bd63831ba)


图1.1 巴法云控制台界面图
在MQTT设备云下面输入主题名称（限英文和数字）后，点右边的“新建主题”按键，创建主题。在下图中记录下自己设备云的私钥，以及设备云中的主题名称。
![image](https://github.com/valueking/effective-workspce-ycc/assets/167502049/d34a63c3-2c9e-4529-b3fe-9dbf441d5760)

图1.2 巴法云MQTT设备创建界面图
创建后，在主题内可以修改消息内容，再点击右边的“推送消息”按钮，订阅的都可以收到消息。
![image](https://github.com/valueking/effective-workspce-ycc/assets/167502049/a53252e4-bf50-4c83-ab08-0773ec5827d4)

图1.3 巴法云MQTT设备主题界面图
当STM32或者其它订阅了主题的设备，修改了主题数值，在上图中可以看到数值的变化，所有订阅者也可以收到消息。

二、ESP8266的使用
1、ESP8266固件进行刷新：要使用MQTT的固件，下载地址如下：
https://docs.ai-thinker.com/%E5%9B%BA%E4%BB%B6%E6%B1%87%E6%80%BB
已经提前下载好的，将ESP8266插上下载板上，使用ESP8266-01S.rar包内的下载软件ESPFlashDownloadTool_v3.6.4.exe进行固件下载。
下图选择ESP8266 DownloadTool后，进入右图。
选择下载的固件后，点下面START开始下载。
![image](https://github.com/valueking/effective-workspce-ycc/assets/167502049/5ff32b6c-d94d-46da-82fe-185bbd774728)
![image](https://github.com/valueking/effective-workspce-ycc/assets/167502049/98e9d72f-fc2a-4990-b8a6-0ef146084225)

2、ESP8266连接MQTT服务器指令
ESP8266      USB-TTL
3V3     	3V3
GND  		GND
RX(TTL)		TXD
TX(TTL)		RXD

连接及发送数据方式说明：MQTT单连接透传(AT模式baud:115200)，注意指令后面都要加上回车换行符，串口调试助手内进行回车，STM中在后面加上’\r’和’\n’(或0x0d,0x0a)。（串口以16进制发送 41 54 0d 0a 对应字符为“AT回车”）
（1）串口给ESP8266发送工作模式的设置指令：
AT+CWMODE=3
ESP8266响应回复：
OK

（2）发送连接路由器WIFI指令（建议用自己手机热点）：
AT+CWJAP="MIX 4","123456789"
等待几秒连接上后，回复：
WIFI CONNECTED
WIFI GOT IP

OK
未连接上回复ERROR

（3）发送MQTT设置（密钥为图1.2中的巴法云密钥）：
AT+MQTTUSERCFG=0,1,"aee3a1ecc6754bc39f486a0459b3ecd0","","",0,0,""
回复：
OK

（4）连接MQTT服务器：
AT+MQTTCONN=0,"bemfa.com",9501,0
回复：
+MQTTCONNECTED:0,1,"bemfa.com","9501","",0

OK

（5）订阅MQTT消息（主题名图1.2中的巴法的主题名）
AT+MQTTSUB=0,"Y",1
回复：
OK

（6）发布MQTT消息（下面的"hello"为发送的测试数据，更改为自己需要的内容，比如
LEDON或者LEDOFF），巴法云上的数值会变为发送的测试数据。
AT+MQTTPUB=0,"Y","succeed to connect",0,0
回复：

OK
+MQTTSUBRECV:0,"Y",18,hello

（7）当巴法云上的数值发生变化，比如变为123，串口会收到下面报文：
+MQTTSUBRECV:0,"Y",3,123
