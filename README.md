# Teltonika IOT Monitoring
 Teltonika RUT/ TRB in IOT Monitoring using MQTT

Teltonika devices are using MQTT as Send and request technique. 

To get some information from the device you should publish a message asking for this specific information as the following:


$ mosquitto_pub -h hostname -p port -u user -P pass -t get/<SERIAL>/command -m uptime

The device will reply by publishing the information on another topic related to this information 

In this case the topic name will be "router/<SERIAL>/uptime" so the client should subscribe to that topic 

$ mosquitto_sub -h hostname -p port -u user -P pass -t router/<SERIAL>/uptime

the information will be published ONLY when requested not periodically

for more information on the concept 
https://wiki.teltonika-networks.com/view/Monitoring_via_MQTT

But, what if you want the device send the status of their Input/Output status on change !!!!

##Step 1

Note: When dealing with tetonika devices you should know that they always update their firmware so...

You should update your device with the latest --working-- firmware

I start the project using RUT950 with very old firmware "RUT9XX_R_00.06.05.3" which doesn't support the features we are going to use.

RUT9XX_R --> is referred to old style WebUI which is black, and the menu is on the top of the screen

RUT9_R --> is referred to new style WebUI which is blue, and the menu is on the left of the screen

1.1 update the device with latest firmware of "RUT9XX_R" which is RUT9XX_R_00.06.08.6 MD5 c80f622fe630fa4e97714e81dcb29cdb 

1.2 update the device with latest firmware of "RUT9_R" which is RUT9_R_00.07.01.2 MD5 7ee1ad8b753895ca3b5c356d0fc71153 released on 2022.01.04 (on the time of writing this article)

You can find the firmware version and releases on the below link:

https://wiki.teltonika-networks.com/view/RUT950_Firmware_Downloads


##Step 2

2.1 Install the packages needed like in our case we will need <bold>MQTT</bold> package to be installed 

    Package "00.07.01.2 - Latest" release date 2022.01.04 MD5 8f267659499a61e7375d62d240120c1c

    For more information on package download and usage:

    https://wiki.teltonika-networks.com/view/RUT950_Package_Downloads

##Step 3

3.1 Ensure your WAN connected to WiredWAN as the next step will require internet connection for the device with stable connection (Don't Use SIM WAN)

3.2 Open Cli of the device or use SSH connection (preferred) 

    Enter the LAN side IP and connect it to your laptop with the same subnet and open SSH client like MobaXterm default username will be root and password will be your device's password

##Step 4

In this step we going to work with OpenWrt which is a Linux operating system targeting embedded devices, this is high level layer on the top of the firmware which provides a fully writable filesystem with package management

For more information https://openwrt.org/start

4.1 Update package manager of the OpenWrt (Require stable internet connection)

    On the Command line enter "opkg update"

4.2 Install mosquito-client which responsible to act as MQTT client installed on the device to send the required information via MQTT to the broker

    On the Command line enter "opkg install mosquitto-client-ssl"

4.3 Create script file which will get the status of the Input/Output and publish it through MQTT

    On the Command line enter "vi /sbin/<anyscriptname>”

    In order to start editing the script press “a” key on your keyboard

    Then type this script:
    
    --
    #!/bin/ash
    ubus call ioman.gpio.din1 status > /tmp/iodata.txt
    mosquitto_pub -h <HOSTNAME> -p <PORT> -m "$(cat /tmp/iodata.txt)" -u <USERNAME> -P <PASSWORD> -t <TOPIC>
    > /tmp/iodata.txt
    --  

    NOTE:
    "ubus call ioman.gpio.din1 status" this line to get the status of Input/Output installed on the board and "ioman.gpio.din1" could be different from model to another if you need to know what is available in your device:

    On the Command line enter "ubus list” will list all available component available in your board
    
    NOTE:
    This script will write the result in a text file and then will send what in the file through MQTT by this ("$(cat /tmp/iodata.txt)")

    The result will be in JSON format as the following:

    { "value": "1", "direction": "in", "bi_dir": false, "invert_input": false }

    For more information about MQTT pub refer to this link: https://mosquitto.org/man/mosquitto_pub-1.html 

    https://wiki.teltonika-networks.com/view/RUT950_Input/Output

4.4 Save the script file.

    After encoding the script press “Esc” key on your keyboard then type “:wq” to save. 

4.5 Change file permissions to allow executable permissions

    On the Command line enter “chmod +x /sbin/<anyscriptname>”

##Step 5

5.1 Configure the Input change state to run the script

    From I/O Juggler create an Action to run the script and create the trigger occurrence based on "Rising", "Failing", or "Both"

    For more information check this: https://wiki.teltonika-networks.com/view/TRB141_Input/Output#Status_from_command_line



        




