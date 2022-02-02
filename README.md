# Teltonika IOT Monitoring
 Teltonika RUT/ TRB in IOT Monitoring using MQTT

Teltonika devices are using MQTT as Send and request technique. 

To get some information from the device you should publish a message asking for this specific information as the following:


$ mosquitto_pub -h hostname -p port -u user -P pass -t get/<SERIAL>/command -m uptime

The device will reply by publishing the information on another topic related to this infomation 
In this case the topic name will be "router/<SERIAL>/uptime" so the client should subscribe to that topic 

$ mosquitto_sub -h hostname -p port -u user -P pass -t router/<SERIAL>/uptime

the infomation will be published ONLY when requested not periodically