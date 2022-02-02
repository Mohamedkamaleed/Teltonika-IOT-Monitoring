# Teltonika IOT Monitoring
 Teltonika RUT/ TRB in IOT Monitoring using MQTT

Teltonika devices are using MQTT as Send and request technique. 

To get some information from the device you should publish a message asking for this specific information as the following:


$ mosquitto_pub -h hostname -p port -u user -P pass -t get/<SERIAL>/command -m uptime

The device will reply by publishing the information on another topic related to this infomation 
In this case the topic name will be "router/<SERIAL>/uptime" so the client should subscribe to that topic 

$ mosquitto_sub -h hostname -p port -u user -P pass -t router/<SERIAL>/uptime

the infomation will be published ONLY when requested not periodically

for more information on the concept 
https://wiki.teltonika-networks.com/view/Monitoring_via_MQTT

But, what if you want the device send the status of thier Input/Output status on change !!!!

##Step 1

Note: When dealing with tetonika devices you should know that they always update thier firmware so...

You should update your device with the latest --working-- firmware

I start the project using RUT950 with very old firmware "RUT9XX_R_00.06.05.3" which doesn't support the features we are gonna use.

RUT9XX_R --> is refered to old style WebUI which is black and the menue is on the top of the screan
RUT9_R --> is refered to new style WebUI which is blue and the menue is on the left of the screan

1.1 update the device with latest firmware of "RUT9XX_R" which is RUT9XX_R_00.06.08.6 MD5 c80f622fe630fa4e97714e81dcb29cdb 
1.2 update the device with latest firmware of "RUT9_R" which is RUT9_R_00.07.01.2 MD5 7ee1ad8b753895ca3b5c356d0fc71153 released on 2022.01.04 (on the time of writing this artical)

You can find the firmware verstion and releases on the below link
https://wiki.teltonika-networks.com/view/RUT950_Firmware_Downloads


##Step 2

2.1 Install the packages needed like in our case we will need <bold>MQTT</bold> package to be installed 

Package "00.07.01.2 - Latest" release date 2022.01.04 MD5 8f267659499a61e7375d62d240120c1c



